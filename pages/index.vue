<template>
  <div class="container">
    <div class="d-flex flex-row justify-content-center">

      <div class="col-md-8">
        <div>
        </div>
        <div class="card-body">
          <ul class="list-group">
            <li class="list-group-item" v-for="(todo, index) in todos" :key="index">
              <a href="#">
                <h1>{{ todo.title }}</h1>
                <p>{{todo.message}}</p>
                <button @click="remove(todo, index)">Remove</button>
              </a>
            </li>
          </ul>

          <form @submit.prevent="sub()">
            <div class="form-group mt-5">
              <input
                type="text"
                placeholder="add title"
                class="form-control"
                v-model="title"
              />
              <input
                type="text"
                placeholder="add message"
                class="form-control"
                v-model="message"
              />

              <button type="submit" class="btn btn-primary-outline mt-3">Add Note</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import * as Firebase from "firebase/app";
import "firebase/firestore";

export default {
  data() {
    return {
      title: "",
      message:"",
      searchKeyword: "",
    };
  },

  mounted() {
    this.$fire.firestore
      .collection("todos")
      .get()
      .then((res) => {
        res.forEach((doc) => {
          this.$store.commit("setTodo", doc.data());
          console.log(doc.data());
        });
      });
  },
  // not sure why I have to name it as `todos()`. not sure where this is pointing to
  computed: {
    todos() {
      return this.$store.state.todos;
    },
  },

  methods: {
    sub() {
      if (this.title && this.message) {
        this.$fire.firestore
          .collection("todos")
          .add({})
          .then((res) => {
            this.$fire.firestore
              .collection("todos")
              .doc(res.id)
              .set({
                title: this.title,
                message: this.message,
                id: res.id,
              })
              .then(() => {
                this.$store.commit("setTodo", { title: this.title, message: this.message, id: res.id });
                this.title = "";
                this.message = "";
              });
          });
      }
    },

    remove(title, index) {
      this.$fire.firestore
        .collection("todos")
        .doc(title.id)
        .delete()
        .then(() => {
          this.$store.commit("removeTodo", index);
        });
    },
  },
};
</script>
