<template>
  <div class="w-1/3 mx-auto translate-y-1/2">
    <t-card>
      <p class="text-2xl font-bold mb-1">Login Dashboard</p>
      <p class="text-gray-500 mb-5">Please, enter your account</p>
      <t-input class="mb-2" placeholder="Email" type="text" />
      <t-input class="mb-2" placeholder="Password" type="password" />
      <div class="flex justify-end mt-4 mb-2">
        <t-button @click="login()">Submit</t-button>
      </div>
    </t-card>
  </div>
</template>

<script>
export default {
  methods: {
    login() {
      this.$router.push("/dashboard");
    },
  },
};
</script>
