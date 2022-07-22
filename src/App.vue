<template>
  <div id="app">
    Hello!
    <div class="flex-style">
      <Drag :pDropzoneId="1" :pList="list1"></Drag>
      <Drag :pDropzoneId="2" :pList="list2"></Drag>
      <Drag :pDropzoneId="3" :pList="list3"></Drag>
    </div>
  </div>
</template>

<script>
import Drag from './components/dragTest.vue'

export default {
  name: 'App',
  components: {
    Drag
  },
  data() {
    return {
      list1: [
        {
          id: '1',
          title: 'item A',
          list: 1,
        },{
          id: '2',
          title: 'item B',
          list: 1,
        },{
          id: '3',
          title: 'item C',
          list: 1,
        },{
          id: '4',
          title: 'item D',
          list: 1,
        },{
          id: '5',
          title: 'item E',
          list: 1,
        },
      ],
      list2: [
        {
          id: '6',
          title: 'item F',
          list: 2,
        },{
          id: '7',
          title: 'item G',
          list: 2,
        },{
          id: '8',
          title: 'item H',
          list: 2,
        },{
          id: '9',
          title: 'item I',
          list: 2,
        },{
          id: '10',
          title: 'item J',
          list: 2,
        },
      ],
      list3: [
        {
          id: '11',
          title: 'item K',
          list: 2,
        },{
          id: '12',
          title: 'item L',
          list: 2,
        },{
          id: '13',
          title: 'item M',
          list: 2,
        },{
          id: '14',
          title: 'item N',
          list: 2,
        },{
          id: '15',
          title: 'item O',
          list: 2,
        },
      ],
    };
  }
}
</script>

<style scoped>
.flex-style {
  display: flex;
  gap: 20px;
  padding: 0 20px;
}
</style>
