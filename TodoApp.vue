<template>
    <input
        type="text"
        v-model="newTask"
        @keyup.enter="addTask"
        placeholder="Add a new task..."
    />
    <p> {{ remaining }} tasks left</p>
    <button @click="filter = 'all'" id="all">All</button>
    <button @click="filter = 'active'" id="active">Active</button>
    <button @click="filter = 'completed'" id="completed">Completed</button>
    <ul>
        <li
        v-for="(task, index) in filteredTasks"
        :key="index"
        >
        <div>
            <input type="checkbox" v-model="task.done" />
            <span :style = "{textDecoration: task.done? 'line-through': 'none'}">
                {{ task.item }}
            </span>
        </div>
        <button @click="removeTask(index)" id="deleteTask">Delete</button>
        </li>
    </ul>
</template>

<script>
    export default {
        data() {
            return {
                newTask: '',
                tasks: [],
                filter: 'all'
            }
        },
        methods: {
            addTask() {
                if (this.newTask.trim() !== '') {
                    this.tasks.push({
                        item: this.newTask.trim(),
                        done: false
                    });
                    this.newTask = ''
                }
            },
            removeTask(index) {
                this.tasks.splice(index, 1)
            }
        },
        computed: {
            remaining() {
                return this.tasks.filter(task => !task.done).length
            },
            filteredTasks() {
                if (this.filter === 'active') {
                    return this.tasks.filter(task => !task.done)
                } else if (this.filter === 'completed') {
                    return this.tasks.filter(task => task.done)
                }
                return this.tasks
            }
        },
        mounted() {
            const saved = localStorage.getItem('tasks')
            if (saved) {
                this.tasks = JSON.parse(saved)
            }
        },
        watch: {
            tasks: {
                handler (newTasks) {
                    localStorage.setItem('tasks', JSON.stringify(newTasks))
                },
                deep: true
            }
        }
    }
</script>

<style scoped>
input[type="text"] {
  padding: 0.5em;
  width: 100%;
  margin-bottom: 1em;
}
li {
  margin-bottom: 0.5em;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
button{
  margin-left: 1em;
  background-color: transparent;
  border: 1px solid #ccc;
  border-radius: 5px;
  cursor: pointer;
}

#deleteTask {
    color: red;
}
</style>