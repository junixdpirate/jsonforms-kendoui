<template>
    <div v-if="control.visible">
        
        <kendo-input :name="control.path" type="email" :label="control.label" @change="onChange">{{ control.data }}</kendo-input>
            
        <div class="error" v-if="control.errors">{{ control.errors }}</div>
    </div>
</template>
  
  <script lang="ts">
  import '@progress/kendo-theme-default/dist/all.css';
 import { ControlElement } from '@jsonforms/core';
import { defineComponent } from 'vue';
import { rendererProps, useJsonFormsControl } from '@jsonforms/vue';
import { Input } from '@progress/kendo-vue-inputs';

const KendoEmailControl = defineComponent({
  name: 'kendo-email-control',
  components: {
      'kendo-input': Input,
    },
  props: {
    ...rendererProps<ControlElement>(),
  },
  setup(props) {
    return useJsonFormsControl(props);
  },
  methods: {
    onChange(event: Event) {        
      this.handleChange(
        this.control.path,
        (event.target as HTMLInputElement).value
      );
    },
  },
});
export default KendoEmailControl;
  </script>