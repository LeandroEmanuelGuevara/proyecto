<template>
    <v-form v-model="formValid" @submit.prevent="submitForm">
      <v-container>
        <v-row>
          <v-col v-for="(field, index) in formFields" :key="index" cols="12" sm="6">
            <v-text-field
              v-if="field.type === 'text' || field.type === 'email' || field.type === 'number'"
              v-model="formData[field.name]"
              :label="field.label"
              :type="field.type"
              :rules="getValidationRules(field)"
              :max-length="field.maxLength"
              :required="field.required"
            />
            <v-select
              v-else-if="field.type === 'selectable'"
              v-model="formData[field.name]"
              :label="field.label"
              :items="field.options"
              :rules="getValidationRules(field)"
              :required="field.required"
            />
          </v-col>
        </v-row>
        <v-btn type="submit" :disabled="!formValid">Enviar</v-btn>
      </v-container>
    </v-form>
  </template>
  
  <script lang="ts">
  import { defineComponent, ref } from 'vue';
  
  interface FormField {
    name: string;
    label: string;
    type: string;
    default: string;
    maxLength?: number;
    required: boolean;
    validations: Array<{ type: string; message: string; value?: any; pattern?: string; rules?: any }>;
    options?: string[];
  }
  
  export default defineComponent({
    name: 'DynamicForm',
    data() {
      return {
        formValid: false,
        formData: {} as Record<string, any>,
        formFields: [
          {
            name: 'nombre',
            label: 'Nombre y Apellido',
            type: 'text',
            default: '',
            required: true,
            maxLength: 50,
            validations: [
              { type: 'required', message: 'El nombre es obligatorio.' },
              { type: 'minLength', value: 3, message: 'El nombre debe tener al menos 3 caracteres.' },
              { type: 'regex', pattern: '^[a-zA-ZáéíóúÁÉÍÓÚ\\s]+$', message: 'El nombre solo puede contener letras y espacios.' },
              { type: 'complex', rules: { noNumbers: true }, message: 'El nombre no debe contener números.' }
            ]
          },
          {
            name: 'mail',
            label: 'Mail',
            type: 'email',
            default: '',
            required: true,
            maxLength: 30,
            validations: [
              { type: 'required', message: 'El mail es obligatorio.' },
              { type: 'regex', pattern: '^[\\w-\\.]+@([\\w-]+\\.)+[\\w-]{2,4}$', message: 'El formato del mail es inválido.' }
            ]
          },
          {
            name: 'codArea',
            label: 'Código de Área',
            type: 'number',
            default: '',
            required: true,
            maxLength: 4,
            validations: [
              { type: 'required', message: 'El código de área es obligatorio.' },
              { type: 'maxLength', value: 4, message: 'El código de área debe tener máximo 4 dígitos.' }
            ]
          },
          {
            name: 'telefono',
            label: 'Teléfono',
            type: 'number',
            default: '',
            required: true,
            validations: [
              { type: 'required', message: 'El teléfono es obligatorio.' },
              { type: 'minLength', value: 7, message: 'El teléfono debe tener al menos 7 dígitos.' }
            ]
          },
          {
            name: 'vivienda',
            label: 'Vivienda',
            type: 'selectable',
            default: '',
            required: true,
            options: ['Casa', 'Departamento'],
            validations: [
              { type: 'required', message: 'La selección de vivienda es obligatoria.' }
            ]
          }
        ]
      };
    },
    methods: {
      getValidationRules(field: FormField) {
        const rules = field.validations.map(validation => {
          switch (validation.type) {
            case 'required':
              return v => !!v || validation.message;
            case 'minLength':
              return v => (v && v.length >= validation.value) || validation.message;
            case 'maxLength':
              return v => (v && v.length <= validation.value) || validation.message;
            case 'regex':
              return v => (v && new RegExp(validation.pattern).test(v)) || validation.message;
            case 'complex':
              if (validation.rules?.noNumbers) {
                return v => !/\d/.test(v) || validation.message;
              }
              return () => true;
            default:
              return () => true;
          }
        });
        return rules;
      },
      submitForm() {
        alert('Formulario enviado!');
        console.log(this.formData);
      }
    }
  });
  </script>
  