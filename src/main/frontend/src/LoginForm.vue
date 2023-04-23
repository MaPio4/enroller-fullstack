<template>
  <form @submit.prevent="enter()">
    <label>Login</label>
    <input type="text" @click="change()" v-model="user.login">
    <label>Hasło</label>
    <input type="password" @click="change()" v-model="user.password">
    <button style="margin-right: 20px;" type="submit">{{ buttonLabel }}</button>

  </form>
</template>

<script>
export default {
  props: {
    buttonLabel: String,
    eventName: String,
  },
  data() {
    return {
      user: {}
    };
  },
  methods: {
    enter() {
      this.$emit(this.eventName, this.user);
    },
    change() {
      this.$emit("hide")
    }
  }
}
</script>
