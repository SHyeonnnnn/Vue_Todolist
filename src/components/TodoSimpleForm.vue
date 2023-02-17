<template>
    <form @submit.prevent="onSubmit">
      <div class="d-flex">
        <div class="flex-grow-1">
          <input type="text" placeholder="오늘 할일을 작성해주세요" ref="input" v-model="todo">
        </div>
        <div class="b-btn">
          <button class="btn" type="submit" @click="focus()">ADD</button>
        </div>
      </div>
      <div v-show="hasError" style="color: red">
        해야할일을 작성,추가해주세요
      </div>
    </form>
</template>
<script>
  import { ref } from 'vue';
  export default {
      setup(props, context){
          const todo = ref('');
          const hasError=ref(false);
          const onSubmit = () => {
              if(todo.value === ""){
                  hasError.value = true;
              }else{
                  context.emit('add-todo',{//emit쓰는방법 2 context.emit
                      id: Date.now(),
                      subject: todo.value,
                      completed:false
                      }
                  )
                  todo.value = "";
                  hasError.value=false;
                  }
          }
          return {
              todo,
              hasError,
              onSubmit
          }
      },
      methods: {
          focus: function()
              {
                  this.$refs.input.focus();
              }
          }
      }
</script>
<style>

</style>