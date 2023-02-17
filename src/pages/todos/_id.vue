<template>
    <div class="container">
        <h1>Todo page</h1>
        <div v-if="loading">
            Loading...
        </div>
        <form v-else @submit.prevent="onSave">
            <div class="row1">
                <div>
                    <div class="form-group">
                        <label>Todo Subject</label>
                        <input type="text" class="form-control" v-model="todo.subject">
                    </div>
                </div>
                <div>
                    <div class="form-group">
                        <label>Status</label>
                        <div>
                            <button @click="toggleTodoStatus" type="button" class="btn" :class="todo.completed || 'btnF'">{{todo.completed ? '완료' : '미완료'}}</button>
                        </div>
                    </div>
                </div>
            </div>
            <button class="btn" type="submit" :disabled="!todoUpdated">저장</button>
            <button class="btn btnl" @click="moveToTodoListPage">취소</button>
        </form>
    </div>
</template>
<script>
    import { useRoute, useRouter } from 'vue-router';
    import axios from 'axios';
    import { ref, computed } from 'vue';
    import _ from 'lodash'
    export default {
        setup() {
            const route=useRoute();
            const router=useRouter();
            const todo=ref('');
            const loading = ref(true);
            const todoId=route.params.id
            const originalTodo = ref('')
            const getTodo = async () => {
                const res = await axios.get(`http://localhost:3000/todos/${todoId}`);
                todo.value = res.data
                originalTodo.value = res.data
                loading.value = false
            }
            const onSave = async () => {
                const res = await axios.put(`http://localhost:3000/todos/${todoId}`, {
                    subject: todo.value.subject,
                    completed: todo.value.completed,
                });
            }
            const todoUpdated = computed(() => {
                return !_.isEqual(todo.value, originalTodo.value)
            })
            const toggleTodoStatus  = () => {// 완료미완료버튼
                todo.value.completed =! todo.value.completed
            }
            const moveToTodoListPage = () => {// 취소버튼
                router.push({
                    name:'Todos'
                })
            }
            getTodo();
            return {
                todo,
                toggleTodoStatus,
                moveToTodoListPage,
                onSave,
                todoUpdated
            }
        }
    }
</script>
<style>
    h1{margin: 30px 0 20px;}
    .form-group{display: flex;flex-direction: column;}
    .form-group label{font-weight: bold;margin-bottom: 10px;}
    .form-control{padding: 10px 20px;}
    .row1{display: flex;justify-content: space-between;}
    .row1>div{flex-basis: 49%;}
    .btnl{margin-left: 20px;background: #ededed; color: #222;}
    .btnl:hover{background: #ffffff; color: #222;}
    .btnl:active{background: #8b8b8b; color: #222;}
</style>