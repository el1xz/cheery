<template>
  <div class="images_wrapper">
    <div class="card_block" v-for="item in items" :key="item.url">
      <div class="img_box">
        <img class="img" :src="item.url" :alt="item.name" />
      </div>
      <div class="tags_wrapper">
        <div class="tag" v-for="tag in item.tags.slice(1)" :key="tag.link">
          <p :href="tag.link">{{ tag.name }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import cheerio from 'cheerio'
import axios from 'axios'


export default {
  data() {
    return {
      items: []
    }
  },
  mounted() {
    axios.get('https://unsplash.com/images').then(response => {
      const html = response.data
      const $ = cheerio.load(html)
      const items = []
      // берем класс "L34o8" и парсим все картинки из него (первые 10 slise)
      $('.L34o8 img').slice(0, 10).each(function (i) {
        // создаем константу с тегами которую преобразовывем в массив чтобы залить туда несколько тегов
        const tags = $(this).closest('.YdIix').find('a').toArray()
        items[i] = {
          url: $(this).attr('src'),
          tags: tags.map(tag => ({
            // записываем данные в массив
            name: $(tag).text(),
            link: $(tag).attr('href')
          }))
        }
      })
      this.items = items
    })
  }
}
</script>

<style>

body {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.images_wrapper {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
}

.card_block {
  width: 450px;
}

.img_box {
  width: 450px;
  height: 450px;
}

.img {
  width: 100%;
  height: 100%;
  object-fit: cover;

}

.tags_wrapper {
  margin-top: 20px;
  margin-bottom: 20px;
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.tag p {
  margin: 0;
  color: #767676;
  background-color: #eee;
  padding: 4px 12px;
  border-radius: 5px;
  cursor: pointer;
  transition: 0.3s;
}

.tag p:hover {
  background-color: rgb(224, 222, 222);
}


</style>
