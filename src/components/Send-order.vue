<script setup>
import { ref } from 'vue';
import { Form, Field, ErrorMessage } from 'vee-validate';
import * as yup from 'yup';

const schema = yup.object({
  email: yup.string().email().required(),
  name: yup.string().required(),
});

const props = defineProps(["products"])

const selectedProduct=ref('')
const name = ref('')
const email = ref('')
const phone = ref('')
const cardMonth = ref('')
const cardNumber = ref('')
const cardYear = ref('')
const cardCvv = ref('')
const cardHolder = ref('')
const resultText = ref('')

function validateEmail(value) {
      // if the field is empty
      if (!value) {
        return 'This field is required';
      }
      // if the field is not a valid email
      const regex = /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i;
      if (!regex.test(value)) {
        return 'This field must be a valid email';
      }
    }
async function submitOrder(){
    const form= new FormData();
    form.append("Product",selectedProduct)
    form.append("Name",name)
    form.append("Email",email)
    form.append("Phone",phone)
    form.append("cardNumber",cardNumber)
    form.append("cardYear",cardYear)
    form.append("cardCvv",cardCvv)
    form.append("cardHolder",cardHolder)

    await fetch(' https://httpbin.org/',{
        method:'Post',
        body:form,
        headers:{
            'Content-Type':'application/json'
        }
    })
    .then(
        response=>{
        response.json()
        resultText.value='Заказ успешно отправлен'})
    .catch(resultText.value='Заказ не отправлен. Попробуйте позднее')
}

</script>

<template>
    <Form :validation-schema="schema">
    <div class="order-form-wrapper">
    <h2>Оформление заказа</h2>

    <div class="form-group">
      <label>Товар</label>
      <select v-model="selectedProduct" class="form-control">
        <option value="">Выберите товар</option>
        <option v-for="product in products" :key="product.id" :value="product.id">
          {{ product.title }} — {{ product.price }} 
        </option>
      </select>
    </div>

    <div class="form-group">
      <label>Имя</label>
      <input
       type="text"
       v-model="name" 
       class="form-control" 
       placeholder="Иван Иванов"
       />
    </div>

    <div class="form-group">
      <label>Email</label>
      <Field
        name="Email"
        type="email"
        />
       <ErrorMessage name="email"/>
    </div>

    <div class="form-group">
      <label>Телефон</label>
      <input type="tel" v-model="phone" class="form-control" placeholder="+7 (999) 123-45-67" />
    </div>

    <div class="card-section">
      <h3>Оплата картой</h3>
      <div class="form-group">
        <label>Номер карты</label>
        <input
          v-model="cardNumber"
          @input="formatCardNumber"
          type="text"
          class="form-control"
          placeholder="1234 5678 9012 3456"
          maxlength="19"
        />
      </div>
      <div class="card-row">
        <div class="form-group">
          <label>Срок действия</label>
          <div class="card-date">
            <select v-model="cardMonth" class="form-control" style="width: 60px">
              <option value="">ММ</option>
              <option v-for="m in 12" :key="m" :value="m < 10 ? '0' + m : m">{{ m < 10 ? '0' + m : m }}</option>
            </select>
            /
            <select v-model="cardYear" class="form-control" style="width: 60px">
              <option value="">ГГ</option>
              <option v-for="y in 10" :key="y" :value="(new Date().getFullYear() + y - 2000).toString().slice(-2)">
                {{ (new Date().getFullYear() + y - 2000).toString().slice(-2) }}
              </option>
            </select>
          </div>
        </div>
        <div class="form-group">
          <label>CVV</label>
          <input
            v-model="cardCvv"
            @input="formatCvv"
            type="text"
            class="form-control"
            placeholder="123"
            maxlength="3"
          />
        </div>
      </div>
      <div class="form-group">
        <label>Имя держателя</label>
        <input
          v-model="cardHolder"
          type="text"
          class="form-control"
          placeholder="IVAN IVANOV"
        />
      </div>
    </div>

    <div class="form-actions">
      <button type="button" @click="submitOrder" class="btn btn-primary">
        Отправить заказ
      </button>
    </div>
 <div v-if="resultText">{{resultText}}</div>
  </div>
</Form>
</template>

<style scoped>
.order-form {
  max-width: 700px;
  margin: 20px auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-family: Arial, sans-serif;
}
.form-actions {
  margin: 20px 0;
}
.form-group {
  margin-bottom: 15px;
}
.form-control {
  width: 50%;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}
label{
    margin-right: 15px;
}
.card-section {
  margin: 20px 0;
  padding: 15px;
  background: #f9f9f9;
  border: 1px dashed #ccc;
  border-radius: 6px;
}
</style>