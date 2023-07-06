<template>
  <v-row justify="center" align-content="center" class="bg-dark">

    <div style="width: 100%; height: auto; padding-right: 400px; position:absolute; padding-top: 50px;" class=" d-flex justify-content-end">
      <span class=" d-flex w-50" id="blob-man" style="width: 50px; height: auto;">
      </span>
    </div>

    <v-slide-y-transition appear>
      <v-card class="d-inline-block mx-auto my-auto" elevation="6"
        :max-width="$vuetify.breakpoint.mdAndUp ? '710' : '300'">
        <!-- @submit.prevent="handleSubmit(onSubmit)" -->
        <v-form @submit.prevent="onSubmit">
          <v-card-text>
            <div class="d-flex  flex-no-wrap justify-space-between">
              <v-col cols="12" md="6" sm="12" class="hidden-sm-and-down" align-self="center">
                <div id="office-man" style="max-width: 400px" />
              </v-col>
              <v-col cols="12" md="6" sm="12">
                <Sim></Sim>
                <v-text-field v-model.trim="form.username" type="text" name="username" class="mt-8"
                  :label="'inputs.Username'" clearable counter :maxlength="45" autocomplete="off" required
                  prepend-icon="mdi-account-circle-outline" />

                <v-text-field v-model.trim="form.password" name="password" color="primary" :label="'inputs.Password'"
                  prepend-icon="mdi-lock-outline" clearable counter :maxlength="32" autocomplete="off" required
                  :type="show ? 'text' : 'password'" :append-icon="show ? 'mdi-eye-off' : 'mdi-eye'"
                  @click:append="show = !show" />


                <v-checkbox v-model="form.remember" type="checkbox" required>
                  <template #label>
                    {{ 'inputs.RememberMe' }}
                    <v-spacer></v-spacer>
                    <!--
                      <nuxt-link
                        :to="localePath('/password/forgot')"
                        class="primary--text caption hidden-sm-and-down"
                      >
                        {{ $t('texts.ForgotPassword') }}
                      </nuxt-link>
                      <v-tooltip top>
                        <template #activator="{ on, attrs }">
                          <v-btn
                            icon
                            color="primary"
                            class="hidden-md-and-up"
                            v-bind="attrs"
                            v-on="on"
                          >
                            <v-icon>mdi-help-circle</v-icon>
                          </v-btn>
                        </template>
                        <span>{{ $t('texts.ForgotPassword') }}</span>
                      </v-tooltip>
                      -->
                  </template>
                </v-checkbox>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn :loading="loading" :disabled="loading" type="submit" color="primary">
                    {{ 'buttons.Login' }}
                  </v-btn>
                </v-card-actions>
                <v-card-actions class="hidden-sm-and-down">
                  <Town></Town>
                  <v-spacer></v-spacer>
                </v-card-actions>
              </v-col>
            </div>
          </v-card-text>
        </v-form>
      </v-card>
    </v-slide-y-transition>
    <v-dialog v-model="dialog" max-width="500">
      <v-card flat>
        <v-card-title>
          <v-icon class="text-center" color="amber" large>mdi-alert</v-icon>
        </v-card-title>
        <v-card-text>
          <p>Acceso para funcionarios con permisos al Portal Contratista</p>
        </v-card-text>
        <v-card-actions class="text-right">
          <v-spacer />
          <v-btn outlined color="primary" @click="dialog = !dialog">OK</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<router lang="yaml">
path: /login
meta:
  title: Login
</router>

<script>
import lottie from 'lottie-web';
import * as office from '../animaciones/lottie/office.json'
import * as blob from '../animaciones/lottie/blob.json'
import Sim from './icons/Sim.vue'
import Town from './icons/Town.vue'
import axios from 'axios';

export default {
  name: 'LoginApp',
  components: {
    Sim,
    Town,
  },
  layout: 'page',
  auth: 'guest',
  middleware: ['token'],
  data() {
    return {
      dialog: false,
      show: false,
      loading: false,
      form: {
        username: null,
        password: null,
        remember: false,
      },
    };
  },
  computed: {
    styles() {
      return this.$vuetify.breakpoint.mdAndUp ? 'min-width: 400px;' : '';
    },
    usernameRules() {
      return [
        (value) => !!value || 'El nombre de usuario es requerido',
        (value) =>
          (value && value.length >= 3 && value.length <= 45) ||
          'El nombre de usuario debe tener entre 3 y 45 caracteres',
      ];
    },
    passwordRules() {
      return [
        (value) => !!value || 'La contraseña es requerida',
        (value) =>
          (value && value.length >= 8 && value.length <= 32) ||
          'La contraseña debe tener entre 8 y 32 caracteres',
      ];
    },
  },
  mounted() {
    lottie.loadAnimation({
      container: document.getElementById('office-man'),
      renderer: 'svg',
      loop: true,
      autoplay: true,
      animationData: office.default,
    });

    lottie.loadAnimation({
      container: document.getElementById('blob-man'),
      renderer: 'svg',
      loop: true,
      autoplay: true,
      animationData: blob.default,
    });
    const that = this;
    setTimeout(function () {
      that.dialog = true;
    }, 500);
  },
  methods: {
    async login() {
    const url = 'https://jacgsaw.com/auth/login';
    const data = {
      user: this.form.username,
      pacc: this.form.password,
      key: '987342748AC',
    };

    try {
      const response = await axios.post(url, data, {
        headers: { 'content-type': 'application/x-www-form-urlencoded' },
      });
      if (response.data.sys.login) {
        alert('Inicio de sesión exitoso');
      } else {
        alert('Datos incorrectos');
      }
    } catch (error) {
      console.error(error);
      alert('Los datos de inicio de sesión son incorrectos');
    }
  },
    onSubmit() {
      this.login();
    },
  },
}
</script>