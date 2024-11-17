<template>
  <div>
    <img class="logo" src="./assets/dicktionary.png" />
    <h1 style="margin-top: 0px;">Dicktionary</h1>

    <el-input v-model="searchQuery" placeholder="Find a word..." @keyup.enter="fetchWordDetails" class="search-input">
      <template #suffix>
        <el-icon @click="fetchWordDetails" class="search-icon">
          <Search />
        </el-icon>
      </template>
    </el-input>

    <div v-if="wordData">
      <h1 class="head">{{ wordData.word }}</h1>

      <el-icon class="play" @click="playAudio">
        <VideoPlay />
      </el-icon>
      <p style="color: green;">{{ wordData.phonetic }}</p>

      <!-- desc bullets -->
      <div v-for="(meaning, index) in wordData.meanings" :key="index">
        <el-divider content-position="left">
          <p class="meaning">{{ meaning.partOfSpeech }}</p>
        </el-divider>
        <ul>
          <li v-for="(definition, defIndex) in meaning.definitions" :key="defIndex">
            {{ definition.definition }}
          </li>
        </ul>

        <span class="meaning">Synonyms:</span>&nbsp;
        <span style="color: green;">
          {{ meaning.synonyms.join(", ") || " No synonyms available" }}
        </span>
      </div>
      <p>
        Source:
        <el-link :href="wordData.sourceUrls" target="_blank">{{ wordData.sourceUrls }}</el-link>
      </p>

    </div>
  </div>
</template>

<script>
import axios from "axios";
import { ElMessage } from 'element-plus';


export default {
  data() {
    return {
      searchQuery: "",
      wordData: null,
    };
  },
  methods: {
    async fetchWordDetails() {
      if (!this.searchQuery) return;
      try {
        const response = await axios.get(
          `https://api.dictionaryapi.dev/api/v2/entries/en/${this.searchQuery}`
        );
        this.wordData = response.data[0];
        console.log(this.wordData);
      } catch (error) {
        console.error("Error fetching word data:", error);
        ElMessage.error('Word not found.')
        this.wordData = null;
      }
    },
    playAudio() {
      if (this.wordData?.phonetics[0]?.audio) {
        const audio = new Audio(this.wordData.phonetics[0].audio);
        audio.play();
      } else {
        ElMessage.error('Audio not available for this word.')
      }
    },
  },
};
</script>