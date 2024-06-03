<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      Id: null,
      title: '',
      content: '',
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('http://localhost:3000/articles');
        articles.value = response.data;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `http://localhost:3008/articles/${form.id}`
          : 'http://localhost:3000/articles';
        const method = form.id ? 'put' : 'post';

        const response = await axios[method](url, form);

        // Assuming successful save, update UI and reset form
        articles.value = articles.value.map((article) =>
          article.id === response.data.id ? response.data : article
        );

        form.id = null;
        form.title = '';
        form.content = '';
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    async function remove(article) {
      try {
        await axios.delete(`http://localhost:3000/articles/${article.id}`);
        articles.value = articles.value.filter((a) => a.id !== article.id);
      } catch (error) {
        console.error('Error deleting article:', error);
      }
    }

    function edit(article) {
      form.Id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, remove };
  },
};
</script>

  <template>
    <div class="background">
    <div>
      <form @submit.prevent="save">
        <input type="text" id="title" v-model="form.title" /><br />
        <textarea v-model="form.content"></textarea><br />
        <button type="submit">Save</button>
      </form>
      <ul>
        <li v-for="article in articles" :key="article.id">
          {{ article.title }}<br />
          {{ article.content }}<br />
          <button @click="edit(article)">Edit</button>
          <button @click="remove(article)">Delete</button>
        </li>
      </ul>
      <button @click="load">Load</button>
    </div>

    </div>
  </template>

  <style scoped>
  html, body, .background {
    height: 100%;
    margin: 0;
  }

  .background {
    background-image: url('./img.jpg');
    background-size: cover; /* Menyesuaikan gambar agar menutupi seluruh latar belakang */
    background-position: center;
    background-repeat: no-repeat;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 20px;
  }

  ul {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  li {
    border-bottom: 1px solid #ccc;
    padding: 10px 0;
  }

  button {
    background-color: #4CAF50;
    color: white;
    padding: 10px 20px;
    border: none;
    cursor: pointer;
    margin-top: 10px;
  }

  button:hover {
    background-color: #45a049;
  }
  </style>
