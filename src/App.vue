<template>
  <div class="row example-wrapper">
    <form @submit="handleSubmit" class="form">
      <div class="col-xs-12 col-sm-12 offset-sm-3 example-col">
        <div class="card">
          <div class="card-header">
            <h3 class="card-title">Profile Form</h3>
          </div>
          <div class="card-body">
            <div class="form-group">
              <k-input :name="'fname'" v-model="fname" :label="'First Name'" :pattern="'[A-Za-z]+'" :minlength="2"
                :validation-message="'First name should only contain alphabetic characters.'" :required="true"></k-input>
            </div>
            <div class="form-group">
              <k-input :name="'lname'" :label="'Last Name'" :pattern="'[A-Za-z]+'" :minlength="2"
                :validation-message="'Last name should only contain alphabetic characters.'" :required="true"></k-input>
            </div>
            <json-forms 
            :data="data" 
            :schema="schema" 
            :uischema="uischema" 
            :renderers="renderers"
            v-model="birthday" @focus="handleChange">
            </json-forms>
            <div class="form-group" v-if="isMinimumAgeReached">
              <k-input :name="'email'" v-model="email" :label="'Email'" pattern="[^\s@]+@[^\s@]+\.[^\s@]+"
                :validation-message="'Please enter a valid email address.'" :required="true"></k-input>
            </div>
          </div>
          <div class="card-footer text-center">
            <kbutton :theme-color="'dark'" :disabled="!isMinimumAgeReached">Next</kbutton>
          </div>
        </div>
      </div>
      <div v-if="success" class="alert alert-success">
        Form submitted!
      </div>
    </form>
  </div>
</template>

<script lang="ts">
import { Input } from '@progress/kendo-vue-inputs';
import '@progress/kendo-theme-default/dist/all.css';
import { JsonForms } from '@jsonforms/vue';
import { Button } from '@progress/kendo-vue-buttons';
import { DatePicker } from '@progress/kendo-vue-dateinputs';
const renderers = [
  {
    tester: (uischema: any) => uischema.type === 'Control' && uischema.scope === '#/properties/birthday/',
    renderer: DatePicker,
  },
  // here you can add custom renderers
];

export default {
  components: {
    'k-input': Input,
    'kbutton':Button,
    DatePicker,
    JsonForms,
  },
  computed: {
    isMinimumAgeReached() {
      const today = new Date();
      const minimumAgeDate = new Date(today.getFullYear() - 18, today.getMonth(), today.getDate());
      const birthday = new Date(this.birthday);
      return birthday <= minimumAgeDate;
    },
  },
  data() {
    return {
      fname:'',
      email: '',
      birthday: new Date(),
      data: {
        birthday: '',
        number: 5,
      },
      schema: {
        properties: {
          birthday: { type: 'string', format: 'date' },
        },
        required: ['birthday']
      },
      uischema: {
        type: 'VerticalLayout',
        elements: [
          {
            type: 'Control', scope: '#/properties/birthday/',
          }
        ],
      },
      success: false,
      renderers: Object.freeze(renderers),
    };
  },
  methods: {
    handleSubmit() {
      this.success = true;
    },
    handleChange() {
      this.data.birthday = this.birthday.toString();
    },
  },
};
</script>

<style scoped>
.example-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.example-col {
  max-width: 400px;
}

.card {
  border: none;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.card-header {
  background-color: #f5f5f5;
  padding: 10px;
  border-bottom: 1px solid #ddd;
}

.card-body {
  padding: 20px;
}

.card-title {
  margin: 0;
}

.form-group {
  margin-bottom: 20px;
}

.text-center {
  text-align: center;
}

.btn-margin {
  margin-top: 10px;
}
</style>
