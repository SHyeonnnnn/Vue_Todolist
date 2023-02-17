<template>
  <div>
    <div v-for="(todo1, index) in todos" :key="todo1.id" class="card">
      <div class="card-body" @click="moveToPage(todo1.id)">
        <div class="form-check">
          <!-- <label class="form-check-label" :style="todo1.completed ? todoStyle : {}"> -->
          <label class="form-check-label" :class="{todoStyle:todo1.completed}"><!-- todo1이 컴플리티드 되면 클래스명에 투두스타일(삭제선)줘라 -->
            <input type="checkbox" class="form-check-input" :checked="todo1.completed" @change="toggleTodo(index, $event)" @click.stop>
            {{ todo1.subject }}
          </label>
        </div>
        <div>
          <button class="btnR" @click.stop="deleteTodo(index)">삭제</button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
  import { useRouter} from 'vue-router'
  export default {
    props: {
        todos:{
            type:Array,
            required: true
        }
    },
    emits: ['toggle-todo','delete-todo'],//emit 쓰는방법1 구조분해할당해준다.

    setup (props, {emit}) {//context대신 {emit}
      const router = useRouter();
      const toggleTodo = (index, event) => {
        emit('toggle-todo', index, event.target.checked)//부모한테 toggle-todo라는 이름으로 건네주는거
      }
      const deleteTodo = (index) => {
        emit('delete-todo', index)
      }
      const moveToPage = (todoId) => {
       // console.log(todoId);
      //  router.push('/todos/' + todoId);
      router.push({
        name: 'Todo',
        params:{
          id: todoId
        }
      })
      }
    return {
        deleteTodo,
        toggleTodo,
        moveToPage,
        router
      }
    }
}
</script>
<style>
    
</style>