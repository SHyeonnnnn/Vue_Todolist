<template>
  <div class="container">
    <h1><font-awesome-icon icon=”hat-wizard” /></h1>
    <h2>TODO-List</h2>
    <input type="text" placeholder="검색" class="form-control" v-model="searchText">
    <TodoSimpleForm @add-todo="addTodo" />
    <div v-if="!todos.length">
      검색결과가없습니다.
    </div>
    <TodoList :todos="todos" @delete-todo="deleteTodo" @toggle-todo="toggleTodo"/><!-- 자식으로 @이름 받아서 ="기능실행" -->
    <br>
    <nav class="nav">
      <ul class="pagination">
        <li v-if="currentPage !== 1" class="page-item"><a @click="getTodos(currentPage-1)" href="#" class="page-link"><i class="fas fa-chevron-left"></i></a></li>
        <li v-for="page in numberofPages" :key="page" class="page-item" >
          <a href="#" @click="getTodos(page)" class="page-link" :class="currentPage === page ? 'active' : ''">{{page}}</a>
        </li>
        <li v-if="numberofPages !== currentPage" class="page-item"><a @click="getTodos(currentPage+1)" href="#" class="page-link"><i class="fas fa-chevron-right"></i></a></li>
      </ul>
    </nav>
  </div>
</template>

<script>

import { computed, ref, watch } from 'vue';
import TodoSimpleForm from '@/components/TodoSimpleForm.vue'
import TodoList from '@/components/TodoList.vue'
import axios from 'axios';

export default {
  components: {
    TodoSimpleForm,
    TodoList
  },

  setup(){
    const todos = ref([ ]);
    const searchText = ref('');
    const error=ref('');
    const limit=5;
    const numberOfTodos = ref(0);//전체항목갯수
    const currentPage = ref(1);//활성페이지 = page
    const numberofPages = computed(() => {
      return Math.ceil(numberOfTodos.value/limit)
    });

// AXIOS GET 로 DB가져오기

    const getTodos = async (page = currentPage.value) => {
      currentPage.value=page;
      try{
        const res = await axios.get(`http://localhost:3000/todos?_sort=id&_order=desc&subject_like=${searchText.value}&_page=${page}&_limit=${limit}`);//?뒤에 옵션
        // console.log(res.headers); total값
        numberOfTodos.value = res.headers['x-total-count']//전체투두항목갯수
        todos.value = res.data;//가져온데이터
      }catch(err){
        console.log(err)
        error.value="찾는문장이없습니다."
      }
    };
    getTodos();// 안에 숫자넣으면 페이지임

// AXIOS POST 방식 DB 넣기

    const addTodo = async (todo) => { //위의 todos에 todo넣는 기능
      // todos.value.push(todo);
      error.value='';
      try{
        await axios.post('http://localhost:3000/todos',{//----------axios방법2 비동기
          subject:todo.subject,
          completed:todo.completed,
        });
        getTodos(); // const res = 삭제하고 이거쓰니까 뒤에추가됨
        // todos.value.push(res.data);
      }catch(err){
        console.log(err)
        error.value="잘못된 입력방식입니다."
      }
    }

// WATCH 검색필터기능 페이지 간섭없이
    watch(searchText, () => {
      getTodos(1);
    })

    
    /* const addTodo = (todo) => { //위의 todos에 todo넣는 기능 -----------------axios방법1 동기식
      // todos.value.push(todo);
      error.value='';
      axios.post('http://localhost:3000/todos',{
        subject:todo.subject,
        completed:todo.completed,
      }).then((res) => {
        console.log(res);
        todos.value.push(res.data);
      }).catch((error) => {
        console.log(error);
        error.value="잘못된 입력방식입니다."
      });
    } */
    
    /* const todoStyle={
      color:'gray',
      textDecoration:'line-through'
    } */

//삭제 기능

    const deleteTodo = async(index) => { //todo삭제기능
      error.value='';
      const id = todos.value[index].id; //내가선택하는대상
      try{
        await axios.delete('http://localhost:3000/todos/' + id);
        // todos.value.splice(index, 1)
        getTodos(1);
      }catch(err){
        console.log(err)
        error.value="삭제에러발생"
      }
    }

// 밑줄처리
    const toggleTodo = async (index, checked) => {
      error.value='';
      const id = todos.value[index].id;
      try{
        await axios.patch('http://localhost:3000/todos/' + id, {
          // completed: !todos.value[index].completed
          completed: checked
        });
        // todos.value[index].completed =! todos.value[index].completed; // 반대값 대입시켜 completed값 변경기능
        todos.value[index].completed = checked // 반대값 대입시켜 completed값 변경기능
      }catch(err){
        console.log(err)
        error.value="수정에러발생"
      }
    }

// 검색필터 기능 페이지간섭발생
   /*  const filteredTodos = computed(() => {
      if(searchText.value){
        return todos.value.filter(todo => {
          return todo.subject.includes(searchText.value)
        })
      }
      return todos.value;
    }) */

    return {
      addTodo,
      deleteTodo,
      todos,
      toggleTodo,
      searchText,
      /* filteredTodos, */
      getTodos,
      numberofPages,
      currentPage,
      /* todoStyle, */
    }
  }
}
</script>

<style>
  *{margin: 0;padding: 0;list-style: none;box-sizing: border-box;text-decoration: none;}
  button{border-radius: 12px;box-shadow: 1px 1px 2px #3f3131;cursor: pointer;}
  .container{width: 100%; max-width: 1024px;margin: 0 auto;padding: 0 20px;}
  h2{text-align: center;color: #36767e;margin-bottom: 50px;margin-top: 50px;font-size: 4rem;text-shadow: 4px 1px 3px #039f61;}
  .d-flex{display: flex;}
  .flex-grow-1{flex-grow: 1;}
  .flex-grow-1 input{width: 98%;padding: 10px 20px;}
  .btn{padding: 10px 30px;border: none; background: #20545b;color: #fff; }
  .btn:hover{background: #3daebd;}
  .btn:active{background: #07cae4;}
  .btnF{padding: 10px 23px;border: none; background: #da8484;color: #fff;}
  .btnF:hover{background: #ea1010;}
  .btnF:active{background: #623b3b;}
  form{margin-bottom: 50px;}
  .card{margin: 10px 0;}
  .card-body{border: 1px solid #ccc; padding: 10px 20px;display: flex;justify-content: space-between;}
  .form-check-input{margin-right: 10px;}
  .todoStyle{text-decoration: line-through;color: gray;}
  .btnR{padding: 4px 20px; border-radius: 6px;background: #da8484; color: #fff; outline: none; border: none;}
  .btnR:hover{background: #ea1010;}
  .btnR:active{background: #623b3b;}
  .form-control{width: 100%;border: 1px solid #ddd; margin-bottom: 10px;padding: 10px 20px;}
  .pagination{display: flex;justify-content: center;}
  .page-item{padding: 10px 0; margin: 0 3px; border: 1px solid #ddd;}
  .page-link{color: #666; padding: 10px;}
  .active{background: #666; color: #fff;}
</style>
