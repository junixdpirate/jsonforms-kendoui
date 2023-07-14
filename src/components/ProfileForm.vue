<script lang="ts">
import { rankWith, isStringControl, formatIs } from '@jsonforms/core';
import { JsonForms, JsonFormsChangeEvent, } from '@jsonforms/vue';
import { vanillaRenderers } from '@jsonforms/vue-vanilla';
import { defineComponent } from 'vue';
import { differenceInYears, parse as parseDate } from 'date-fns'
import KendoInputControl from './KendoInputControl.vue';
import KendoEmailControl from './KendoEmailControl.vue';
import KendoDateControl from './KendoDateControl.vue';

const renderers = [
  ...vanillaRenderers,
  // here you can add custom renderers
  { tester : rankWith(2, formatIs('string')), renderer : KendoInputControl},
  { tester : rankWith(2, formatIs('email')), renderer : KendoEmailControl},
  { tester : rankWith(3, formatIs('date')), renderer : KendoDateControl}
];

export default defineComponent({
  components: {
    JsonForms
  },
  
  data() {
    return {
      renderers: Object.freeze(renderers),

      formData: {

        firstName : "",
        lastName : "",
        birthday : "2023-07-13",
        email : "",
        age : 17
      },

        step : 1,        
        ageAllowed : 18,

      formSchema: {

        properties: {          
          firstName : {
            type : 'string',
            format : 'string',
            label : "First Name"
          },
          lastName : {
            type : 'string',
            format : 'string',
            label : 'Last Name'
          },
          birthday : {
            type: 'string',
            format: 'date'
          },
          email : {
            type : 'string',
            format : 'email',
            label : 'Email'
          },
          age : {
            type : 'number'
          }
        },
      },

    
    formUischema: {
        type: 'VerticalLayout',
        elements: [          
          {
            type: 'Control',
            scope: '#/properties/firstName',
          },
          {
            type: 'Control',
            scope: '#/properties/lastName',
          },
          {
            type: 'Control',
            scope: '#/properties/birthday',
          },
          {
            type: 'Control',
            scope: '#/properties/email',
            rule: {
                "effect": "SHOW",
                "condition": {
                "scope": "#/properties/age",
                "schema": {
                        "minimum": 18
                    }
                }
            }
          },
        ],
      },

    };
  },
  methods: {
    onChange(event : JsonFormsChangeEvent) {      
      this.formData = event.data
      this.formData.age = differenceInYears( new Date(), parseDate(this.formData.birthday, "yyyy-MM-dd", new Date()) )
    },

    nextForm() {
        this.step = 2;
    }
  },

  computed : {
    enabledNextStep() : boolean {
        return this.formData.age >= this.ageAllowed
    }
  }
});
</script>

<template>

  <div v-if="step==1">

    <h3>Profile form</h3>

    <json-forms
        :data="formData"
        :schema="formSchema"
        :uischema="formUischema"
        :renderers="renderers"
        @change="onChange"
    />

    <br />

    <button :disabled="!enabledNextStep" @click="nextForm">Next</button>

    <br />
    <br />
    <div>You must be 18 years old or above to proceed.</div>

  </div>

  <div v-if="step==2">
        <h1>Hurry! form submitted!</h1>
        <pre>{{formData}}</pre>
  </div>

</template>