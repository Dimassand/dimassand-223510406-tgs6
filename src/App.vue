<template>
  <div class="container">
    <form class="form" @submit.prevent="save">
      <input
        type="text"
        v-model="form.title"
        placeholder="Title"
        class="input"
      /><br />
      <textarea
        v-model="form.content"
        placeholder="Content"
        class="textarea"
      ></textarea
      ><br />

      <button type="submit" class="button">
        <div class="svg-wrapper-1">
          <div class="svg-wrapper">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              width="30"
              height="30"
              class="icon"
            >
              <path
                d="M22,15.04C22,17.23 20.24,19 18.07,19H5.93C3.76,19 2,17.23 2,15.04C2,13.07 3.43,11.44 5.31,11.14C5.28,11 5.27,10.86 5.27,10.71C5.27,9.33 6.38,8.2 7.76,8.2C8.37,8.2 8.94,8.43 9.37,8.8C10.14,7.05 11.13,5.44 13.91,5.44C17.28,5.44 18.87,8.06 18.87,10.83C18.87,10.94 18.87,11.06 18.86,11.17C20.65,11.54 22,13.13 22,15.04Z"
              ></path>
            </svg>
          </div>
        </div>
        <span>Save</span>
      </button>
    </form>

    <ul class="article-list">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <strong>{{ article.title }}</strong
        ><br />
        {{ article.content }}<br />

        <div class="button-container">
          <button @click="edit(article)" class="button2">Edit</button>
          <button @click="deleteArticle(article.id)" class="button2">
            Delete
          </button>
        </div>
      </li>
    </ul>

    <button @click="load" class="button1 load-button">Load</button>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from "vue";
import axios from "axios";

export default {
  setup() {
    const form = reactive({
      id: "",
      title: "",
      content: "",
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get("http://localhost:3000/articles");
        articles.value = response.data;
      } catch (error) {
        console.error("Error loading articles:", error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `http://localhost:3000/articles/${form.id}`
          : "http://localhost:3000/articles";
        const method = form.id ? "put" : "post";
        const response = await axios[method](url, {
          title: form.title,
          content: form.content,
        });

        if (form.id) {
          articles.value = articles.value.map((article) =>
            article.id === response.data.id ? response.data : article
          );
        } else {
          articles.value.push(response.data);
        }

        resetForm();
      } catch (error) {
        console.error("Error saving article:", error);
      }
    }

    function resetForm() {
      form.id = "";
      form.title = "";
      form.content = "";
    }

    async function deleteArticle(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter((article) => article.id !== id);
      } catch (error) {
        console.error("Error deleting article:", error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, deleteArticle, load };
  },
};
</script>

<style scoped>
.container {
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Helvetica Neue', sans-serif;
  background-color: #f9f9f9;
  border-radius: 10px;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.08);
  transition: all 0.3s ease;
}

.form {
  margin-bottom: 20px;
}

.input,
.textarea {
  width: 100%;
  padding: 15px;
  margin-bottom: 10px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 16px;
  transition: all 0.3s ease;
}

.input:focus,
.textarea:focus {
  border-color: #007bff;
  box-shadow: 0 0 10px rgba(0, 123, 255, 0.1);
}

.button {
  display: flex;
  align-items: center;
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  transition: all 0.3s ease;
}

.button:hover {
  background-color: #0056b3;
  transform: translateY(-2px);
}

.button:active {
  transform: translateY(0);
}

.article-list {
  list-style-type: none;
  padding: 0;
}

.article-item {
  background-color: white;
  padding: 15px;
  border: 1px solid #ddd;
  border-radius: 8px;
  margin-bottom: 10px;
  transition: all 0.3s ease;
}

.article-item:hover {
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.05);
}

.article-item strong {
  display: block;
  font-size: 18px;
  margin-bottom: 5px;
}

.button-container {
  display: flex;
  justify-content: space-between;
}

.load-button {
  display: block;
  width: 100%;
  margin-top: 20px;
  padding: 10px 0;
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  transition: all 0.3s ease;
}

.load-button:hover {
  background-color: #218838;
  transform: translateY(-2px);
}

.load-button:active {
  transform: translateY(0);
}

.button2 {
  padding: 5px 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.3s ease;
}

.button2:hover {
  background-color: #0056b3;
  transform: translateY(-2px);
}

.button2:active {
  transform: translateY(0);
}
</style>
