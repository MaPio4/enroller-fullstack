<template>
  <div id="app">
    <h1>Witaj w systemie do zapisów na zajęcia</h1>
    <button :class="loginPanelActive ? `buttonSelected` : ``"  @click="openLoginPanel()">Zaloguj</button>
    <button :class="!loginPanelActive ? `buttonSelected` : ``"  @click="openRegisterPanel()">Zarejestruj</button>

    <div v-if="authenticatedUsername">
      <UserPanel :username="authenticatedUsername" @logout="logMeOut()"></UserPanel>
      <MeetingsPage :username="authenticatedUsername"></MeetingsPage>
    </div>

    <div v-else>
      <div v-if="message.length > 0" :class="isError ? `box error` : `box message`"> {{ message }}</div>
      <p v-if="loginPanelActive" class="title">Logowanie:</p>
      <p v-else class="title">Rejestracja:</p>
      <LoginForm v-if="loginPanelActive" @login="(user) => logMeIn(user)" @hide ="hideMessage()" button-label='Zaloguj' event-name="login"></LoginForm>
      <LoginForm v-else @register="(user) => registerUser(user)" @hide ="hideMessage()" button-label='Zarejestruj' event-name="register"></LoginForm>
    </div>

  </div>
</template>

<script>
import "milligram";
import axios from "axios";
import LoginForm from "./LoginForm";
import UserPanel from "./UserPanel";
import MeetingsPage from "./meetings/MeetingsPage";

export default {
  components: {LoginForm, MeetingsPage, UserPanel},
  data() {
    return {
      authenticatedUsername: '',
      loginPanelActive: true,
      message: '',
      isError: false,
      timeoutId: null,
    }
  },
  methods: {
    logMeIn(user) {
      this.authenticatedUsername = user.login;
    },
    setError(p_error) {
      this.message = p_error;
      this.isError = true;
    },
    setMessage(p_message) {
      this.message = p_message;
      this.isError = false;
    },
    hideMessage() {
      this.message = ``;
    },
    openLoginPanel() {
      this.hideMessage();
      this.loginPanelActive = true;
    },
    openRegisterPanel() {
      this.hideMessage();
      this.loginPanelActive = false;

    },
    logMeOut() {
      this.authenticatedUsername = '';
    },
    registerUser(user) {
      axios.post('/api/participants', user)
        .then(response => {
            console.log("REGISTERED SUCCESSFULLY");
            this.setMessage("Udało się zarejestrować.");
        })
        .catch(response => {
          if (response.response) {
            if (response.response.status == 409) {
              this.setError("Podana nazwa jest już zajęta.");
              return;
            }
          }
          console.log("ERROR WHILE REGISTERING");
        });
}
  }
}
</script>

<style>
#app {
  max-width: 1000px;
  margin: 0 auto;
}

button {
  margin-right: 20px;
}

.buttonSelected {
  background-color: rgb(159, 142, 175);
}



.box {
  height: 30px;
  border-radius: 10px;
  border-color: black;
  color: rgb(0, 0, 0);
  font-size: 18px;
  font-weight: bold;
  padding-left: 10px;
  padding-right: 10px;
  width: fit-content;
}

.message {
  background-color: rgb(62, 184, 78);
}

.error {
  background-color: rgb(180, 75, 75);
}

.title {
  font-size: 32px;
  font-weight: bold;
}
</style>
