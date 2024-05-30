<script setup>
import { onMounted, onUnmounted, ref } from 'vue';
import JSONDATA from './word.json'
const showApp = ref(true)
const second = ref(0)
const currentIndex = ref(0)
const text = ref('')
const successWord = ref([])
const failedWord = ref([])


const eventFunc = (event) => {
  if(event.key == 'Backspace'){
    text.value = text.value.substring(0, text.value.length - 1)
    return
  }

  if(event.code == 'Space') {
    if(text.value == randomWords.value[currentIndex.value]){ 
        successWord.value.push(randomWords.value[currentIndex.value])
      }
      else{
        failedWord.value.push(randomWords.value[currentIndex.value])
      }
      currentIndex.value = currentIndex.value + 1
      text.value = ''
      return


  }
  text.value = text.value + event.key

}

let counter ;
const startTimer = (time) => {
  counter = setInterval(timer, 1000)

  function timer(){
  
    second.value = time
    time--
    if(time < 0){
      clearInterval(counter)
      showApp.value = false
      second.value = 'Süre Bitti'
    }
   
  }
}

function stop (){
  showApp.value  = !showApp.value
  clearInterval(counter)
  second.value = 'Süre Bitti' 
}

function replay (){
  showApp.value  = !showApp.value
  startTimer(60)

}

function shuffle(a) {
    for (let i = a.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [a[i], a[j]] = [a[j], a[i]];
    }
    return a;
}

const randomWords = ref(shuffle(JSONDATA).slice(0,20))

onMounted(() => {
  window.addEventListener('keydown', eventFunc)
  startTimer(60),
  fetchData
})

onUnmounted(() => {
  window.removeEventListener('keydown', eventFunc)
})

</script>

<template>
  <header class="bg-[#190019] py-8 px-4 text-3xl text-white font-bold">
    <h1>10 FAST FINGER APP</h1>
  </header>
  <div class="container mx-auto py-36 flex flex-col gap-4 ">
    <div class="flex flex-row items-center justify-end gap-4 text-xl " >
      <i class="fa-solid fa-stop p-3 rounded-md bg-white text-[#d03872] replay cursor-pointer" @click="stop" v-if="showApp" ></i>
      <i class="fa-solid fa-retweet p-3 rounded-md bg-white text-[#d03872] replay cursor-pointer" @click="replay" v-if="!showApp" ></i>
      <span class="bg-[#d03872] p-2 rounded-md text-white time" >{{second}}</span>
    </div>
    <div class="bg-white text-2xl font-serif gap-2 flex flex-wrap box-border rounded-md words " v-if="showApp">
      <span v-for="(word, index) in randomWords" :key="index"
      class=" px-4 m-2 py-2 text-3xl"
      :class="{
        '!bg-blue-500 text-white': index == currentIndex,
        'text-red-500':failedWord.includes(word),
        '!bg-red-500': (text && index == currentIndex && !word.startsWith(text) && text.length > 0 ),
        '!text-green-500': successWord.includes(word),
        }"
      > 
        <span v-for="(letter, letterIndex) in word" :key="letterIndex"
        :class="{

        }"
        >{{ letter }}</span>
      </span>

    </div>
    <div class="flex justify-center" v-if="showApp">
      <span class=" px-4 w-3/4 rounded-md bg-white h-12 justiy-center pt-3" > {{ text }}</span>
      <!-- <input type="text" class="p-3 px-4 w-3/4 rounded-md" placeholder="text here" :value="text"> -->
    </div>
    
   
  </div>
  <div class="container mx-auto flex flex-col gap-4 justify-center items-center w-1/4" v-if="!showApp">
    <div class="bg-white w-full p-2  rounded-md flex justify-between text-[#d03872] font-bold italic">KLAVYE HIZ TESTİ SONUCU</div>
    <div class="bg-white w-full p-2 rounded-md flex justify-between">Toplam yazılan kelime <span class="bg-[#d03872] px-2 rounded-full text-white font-bold">{{ currentIndex+1 }}</span></div>
    <div class="bg-white w-full p-2 rounded-md flex justify-between">Doğru yazılan kelime <span class="bg-green-500 px-2 rounded-full text-white font-bold">{{ successWord.length }}</span></div>
    <div class="bg-white w-full p-2 rounded-md flex justify-between">Yanlış yazılan kelime <span class="bg-red-500 px-2 rounded-full text-white font-bold">{{ failedWord.length }}</span></div>
  </div>
</template>

