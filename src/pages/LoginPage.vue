<template>
  <q-page id="login-page" class="flex flex-center">
    <q-card class="q-pa-md no-shadow login-card" bordered>
      <q-btn flat color="grey" icon="sym_o_arrow_back_ios_new" @click="router.go(-1)" />
      <q-form ref="form" class="q-gutter-md" @submit="submit">
        <q-card-section class="text-center">
          <div class="text-h5 text-weight-bold">{{ $t('Sign in') }}</div>
          <div class="">{{ $t('Sign in below to access the admin dashboard.') }}</div>
        </q-card-section>
        <q-card-section>
          <q-input v-model="user.password" filled type="password" label="Password"></q-input>
        </q-card-section>
        <q-card-section>
          <q-btn rounded color="primary" label="Sign in" class="full-width" type="submit"></q-btn>
        </q-card-section>
        <q-card-section class="text-center">
          <div class="">
            {{ $t('Looking for the default password or issues signing in?') }}
            <a
              href="https://photobooth-app.org/setup/configuration/admincenter/"
              target="_blank"
              class="text-weight-bold"
              style="text-decoration: none"
            >
              {{ $t('Check the documentation.') }}
            </a>
          </div>
        </q-card-section>
      </q-form>
    </q-card>
  </q-page>
</template>

<script setup lang="ts">
import { reactive } from 'vue'
import { login } from 'src/util/auth'
import { useRouter } from 'vue-router'

const router = useRouter()

const user = reactive({
  username: 'admin', // there are no usernames in our app, it's always admin...
  password: null,
})

const submit = async () => {
  try {
    await login(user)
    router.push('/admin')
  } catch (err) {
    console.error(err)
  }
}
</script>
