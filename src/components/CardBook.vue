<template>
  <t-card class="min-w-[260px] w-1/6 mb-2">
    <p
      class="font-bold text-2xl mb-1"
      :class="availableCount == 0 ? 'text-gray-400' : ''"
    >
      {{ title }}
    </p>
    <p class="text-gray-500 mb-2">
      Category : {{ category?.name }} <br />
      ISBN : {{ isbn }} <br />
      Published Date : {{ published_date }}
    </p>
    <p class="mb-1 font-bold" v-if="description">Short Description</p>
    <p class="text-gray-500 mb-2">{{ description }}</p>
    <hr class="mb-2" />
    <p class="text-sm">Quantity : {{ quantity }}</p>
    <p class="text-sm text-green-600">Available : {{ availableCount }}</p>
    <p class="text-sm text-red-500">
      Taken : {{ borrowed_count }}
      <!-- <span class="underline cursor-pointer">
      </span> -->
    </p>
    <div class="flex justify-between gap-3 mt-3 mb-2">
      <t-button @click="opemModalBorrower()">View Borrower</t-button>
    </div>

    <t-modal v-model="modalBorrower">
      <table class="w-full">
        <thead>
          <tr>
            <td class="p-3 border-b border-gray-400">User</td>
            <td class="p-3 border-b border-gray-400">Date</td>
            <td class="p-3 border-b border-gray-400">Due Date</td>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in borrowers" :key="index">
            <td class="p-3 border-b border-gray-400">{{ item.user_id }}</td>
            <td class="p-3 border-b border-gray-400">{{ item.created_at }}</td>
            <td class="p-3 border-b border-gray-400">{{ item.due_date }}</td>
          </tr>
        </tbody>
      </table>
    </t-modal>
  </t-card>
</template>

<script>
export default {
  props: {
    id: Number,
    title: String,
    isbn: String,
    description: String,
    published_date: String,
    category: Object,
    quantity: {
      type: Number,
      default: 0,
    },
    borrowed_count: {
      type: Number,
      default: 0,
    },
  },
  data() {
    return {
      modalBorrower: false,
      borrowers: [],
    };
  },
  computed: {
    availableCount() {
      return this.quantity - this.borrowed_count;
    },
  },
  methods: {
    async opemModalBorrower() {
      const { data } = await this.$axios.get(`/books/${this.id}/borrower`);
      if (data.success) {
        this.borrowers = data.data;
        this.modalBorrower = true;
      }
    },
    closeModalBorrower() {
      this.modalBorrower = false;
    },
  },
};
</script>
