<template>
  <div>
    <div class="mb-5 flex justify-between">
      <p class="text-2xl font-bold">Welcome back,</p>
      <p class="text-2xl">Logout</p>
    </div>

    <div class="flex gap-3 mb-5">
      <t-button @click="openModalBorrow()">Borrow Book</t-button>
      <t-button @click="openModalReturn()">Return Book</t-button>
      <t-button>New User</t-button>
      <t-button @click="openModalBook()">New Book</t-button>
    </div>
    <p class="font-bold text-3xl text-blue-600 mb-3">List of Books</p>
    <div class="flex gap-3 flex-wrap">
      <CardBook v-for="(item, index) in books" v-bind="item" :key="index">
      </CardBook>
    </div>

    <t-modal v-model="modalBorrow">
      <div class="flex flex-col gap-2 mb-1">
        <p>User</p>
        <t-select
          placeholder="Select User"
          v-model="activeBorrow.user_id"
          :options="optionUsers"
        />
      </div>
      <div class="flex flex-col gap-2 mb-1">
        <p>Book</p>
        <t-select
          placeholder="Select Book"
          v-model="activeBorrow.book_id"
          :options="optionBooks"
        />
      </div>
      <div class="flex flex-col gap-2 mb-1" v-if="!isReturn">
        <p>Due Date</p>
        <t-input
          type="date"
          placeholder="Select Due Date"
          v-model="activeBorrow.due_date"
        />
      </div>
      <div class="flex flex-col gap-2 mb-1" v-if="isReturn">
        <p>Returned Date</p>
        <t-input
          type="date"
          placeholder="Select Returned Date"
          v-model="activeBorrow.returned_date"
        />
      </div>
      <div class="flex justify-end mt-4">
        <t-button v-if="!isReturn" @click="submitBorrowBook()">Submit</t-button>
        <t-button v-if="isReturn" @click="submitReturnBook()">Submit</t-button>
      </div>
    </t-modal>

    <!-- modal book  -->

    <t-modal v-model="modalBook">
      <div class="flex flex-col gap-2 mb-1">
        <p>Title</p>
        <t-input
          type="text"
          placeholder="Select Title"
          v-model="activeBook.title"
        />
      </div>
      <div class="flex flex-col gap-2 mb-1">
        <p>ISBN</p>
        <t-input
          type="text"
          placeholder="Select ISBN"
          v-model="activeBook.isbn"
        />
      </div>
      <div class="flex flex-col gap-2 mb-1">
        <p>Author</p>
        <t-input
          type="text"
          placeholder="Select Author"
          v-model="activeBook.author"
        />
      </div>
      <div class="flex flex-col gap-2 mb-1">
        <p>Category</p>
        <t-select
          placeholder="Select Category"
          v-model="activeBook.category_id"
          :options="optionCategories"
        />
      </div>
      <div class="flex flex-col gap-2 mb-1">
        <p>Published Date</p>
        <t-input
          type="date"
          placeholder="Select Published Date"
          v-model="activeBook.published_date"
        />
      </div>
      <div class="flex flex-col gap-2 mb-1">
        <p>Short Description</p>
        <t-textarea v-model="activeBook.description"></t-textarea>
      </div>
      <div class="flex flex-col gap-2 mb-1">
        <p>Quantity</p>
        <t-input
          type="number"
          placeholder="Select Quantity"
          v-model="activeBook.quantity"
        />
      </div>
      <div class="flex justify-end mt-4">
        <t-button @click="submitBook()">Submit</t-button>
      </div>
    </t-modal>
  </div>
</template>

<script>
import CardBook from "@/components/CardBook.vue";
export default {
  components: {
    CardBook,
  },
  data() {
    return {
      isReturn: false,
      modalBook: false,
      modalBorrow: false,
      activeBorrow: {
        user_id: null,
        book_id: null,
        due_date: null,
        returned_date: null,
      },
      activeBook: {
        title: "",
        author: "",
        category_id: null,
        description: "",
        quantity: 0,
        isbn: "",
        published_date: "",
      },
      books: [],
      categories: [],
      users: [],
    };
  },
  mounted() {
    this.fetchData();
  },
  computed: {
    optionUsers() {
      return this.users.map((item) => ({ text: item.name, ...item }));
    },
    optionCategories() {
      return this.categories.map((item) => ({ text: item.name, ...item }));
    },
    optionBooks() {
      return this.books.map((item) => ({
        text: `${item.title} - ${item.isbn}`,
        ...item,
      }));
    },
  },
  methods: {
    async fetchData() {
      const booksData = await this.$axios.get("/books");
      if (booksData.data) {
        this.books = booksData.data.data;
      }
      const usersData = await this.$axios.get("/users");
      if (usersData.data) {
        this.users = usersData.data.data;
      }
      const categoriesData = await this.$axios.get("/categories");
      if (categoriesData.data) {
        this.categories = categoriesData.data.data;
      }
    },
    async submitBorrowBook() {
      const { data } = await this.$axios.post(
        "/borrow-books",
        this.activeBorrow
      );
      if (data?.success) {
        await this.fetchData();
        this.modalBorrow = false;
      }
    },
    async submitReturnBook() {
      const payload = { ...this.activeBorrow };
      delete payload.due_date;

      const { data } = await this.$axios.post(
        "/borrow-books/return-book",
        payload
      );
      if (data?.success) {
        await this.fetchData();
        this.modalBorrow = false;
      }
    },
    async submitBook() {
      const { data } = await this.$axios.post("/books", this.activeBook);
      if (data?.success) {
        await this.fetchData();
        this.modalBook = false;
      }
    },
    async openModalBorrow() {
      this.isReturn = false;
      this.modalBorrow = true;
    },
    async openModalReturn() {
      this.isReturn = true;
      this.modalBorrow = true;
    },
    async openModalBook() {
      this.modalBook = true;
    },
  },
};
</script>
