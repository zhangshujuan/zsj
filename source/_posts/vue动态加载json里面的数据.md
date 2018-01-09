---
title: vue动态加载json里面的数据
date: 2018-01-09 15:30:31
tags:
     - vue
---
``` bash
<template>
  <div class="emoji">
    <ul class="emoji-container">
      <li v-for='(emojiGroup,indexOne) in EmojiValues' :key='indexOne' style="padding:0" v-if='indexOne === activeIndex' >
        <a href="javascript:;" v-for='(emoji,index) in emojiGroup' :key='index' @click='selectItem(index, indexOne)'>
          <span class="emoji-item" :title='EmojiKeys[indexOne][index]'>
             <img :src="require('./assets/'+EmojiValues[indexOne][index])">
          </span>
        </a>
      </li>
    </ul>
    
     <ul class="emoji-controller">
      <li v-for="(pannel,index) in pannels" :key="index" @click="changeActive(index)" :class="{'active': index === activeIndex}"></li>
    </ul>  

  </div>

</template>
<script>

import data from '../assets/js/emoji'

export default {
  name: 'emoji',
  data () {
    return {
      emojiDataTwo: data,
      pannels: ['1', '2', '3'],
      activeIndex: 0
    }
  },
  methods: {
    changeActive (index) {
      this.activeIndex = index
    },
    selectItem (index, indexOne) {
      let title = this.EmojiKeys[indexOne][index];
      this.$emit('select', title, indexOne, index)
    }
  },
  computed: {
    EmojiKeys () {
      return this.pannels.map(item => {
        return Object.keys(this.emojiDataTwo[item])
      })
    },
    EmojiValues () {
      return this.pannels.map(item => {
        return Object.values(this.emojiDataTwo[item])
      })
    }
  }
}
</script>
```