<template>
  <q-page padding>
    <q-btn @click="access">Ingresar</q-btn>
    <q-btn @click="createLink">Crear Link</q-btn>
    {{ token }} - {{ expiresIn }}
  </q-page>
</template>

<script setup>
import { api } from 'src/boot/axios';
import { ref } from 'vue';
const token = ref("");
const expiresIn = ref("")
const access =  async () =>{
  try {
     const res = await api.post("/auth/login",{
      email:"mateo.test@gmail.com",
      password: "123456"
    });
    console.log(res.data);
    token.value = res.data.token;
    expiresIn.value = res.data.expiresIn;
    setTime();
  } catch (error) {
    console.log(error);
  }
};
const setTime = () => {
        setTimeout(() => {
            refreshToken();
        }, expiresIn.value * 1000 - 6000);
    };

const refreshToken = async () => {
  try {
    const res = await api.get("/auth/refresh");
    console.log(res.data);
    token.value = res.data.token;
    expiresIn.value = res.data.expiresIn;
    setTime();
  } catch (error) {
     console.log(error);
  }
}
refreshToken();

const createLink = async () => {
  try {
    const res = await api( {
      method: "POST",
            url: "/links",
            headers: {
                Authorization: "Bearer " + token.value,
            },
            data: {
                longLink: "https://axios-http.com/docs/req_config",
            },
    })
    console.log(res.data);
  } catch (error) {
    console.log(error);
  }
}

</script>
