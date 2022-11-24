<template>
  <h1>Element Plus + vee-validate</h1>

  <Form as="el-form" :validation-schema="schema" @submit="onSubmit">
    <!-- You can use the field component to wrap a 'el-form-item' component -->
    <!-- Do this if you have only one or a few places that need validation -->
    <!-- Note that `element-plus` has its own validation and -->
    <!-- we disabled `validate-event` to avoid having the 2 validation logics conflicting with one another  -->
    <Field name="email" v-slot="{ value, field, errorMessage }">
      <el-form-item :error="errorMessage" label="Email" required>
        <el-input
          placeholder="Email Address"
          v-bind="field"
          :validate-event="false"
          :model-value="value"
        />
      </el-form-item>
    </Field>

    <!-- Or compose vee-validate's validation into your element form components -->
    <!-- Do this if you plan to have validation frequently throughout your app -->
    <InputWithValidation label="Full name" name="fullName" />
    <InputWithValidation label="Password" name="password" type="password" />

    <div>
      <!-- The same wrapping technique can be applied to special form elements like checkboxs but with some variations -->
      <Field
        name="terms"
        type="checkbox"
        :value="true"
        :unchecked-value="false"
        v-slot="{ value, errorMessage, handleChange }"
      >
        <el-form-item :error="errorMessage">
          <el-checkbox :model-value="value" @update:model-value="handleChange">
            Agree to terms and conditions
          </el-checkbox>
        </el-form-item>
      </Field>
    </div>

    <div>
      <!-- Even better with the composition API -->
      <CheckboxWithValidation
        name="subscribed"
        label="Subscribe to latest news"
      />
    </div>

    <div>
      <el-button type="primary" native-type="submit">Submit</el-button>
    </div>
  </Form>
</template>

<script setup lang="ts">
import { Field, Form } from 'vee-validate';
import * as yup from 'yup';
import InputWithValidation from './components/InputWithValidation.vue';
import CheckboxWithValidation from './components/CheckboxWithValidation.vue';

const schema = yup.object({
  email: yup.string().required().email().label('Email address'),
  fullName: yup.string().required().label('Full name'),
  password: yup.string().required().min(6).label('Password'),
  terms: yup
    .boolean()
    .required()
    .isTrue('You must agree to terms and conditions')
    .label('terms agreement'),
  subscribed: yup
    .boolean()
    .required()
    .isTrue('This is unusal but you have to subscribe')
    .label('subscription agreement'),
});

function onSubmit(values, actions) {
  console.log(JSON.stringify(values, null, 2));
  actions.resetForm();
}
</script>

<style>
body {
  font-family: Helvetica Neue, Helvetica, PingFang SC, Hiragino Sans GB,
    Microsoft YaHei, SimSun, sans-serif;
}
</style>
