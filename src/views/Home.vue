<template>
    <div v-show="showAddTask">
        <AddTask @add-task="addTask" />
    </div>
    <Tasks @toggle-reminder="toggleReminder" @delete-task="deleteTask" :tasks="tasks"></Tasks>
</template>



<script>
import AddTask from '../components/AddTask.vue';
import Tasks from '../components/Tasks.vue';

export default {
    name: 'Home',
    components: {
        Tasks,
        AddTask
    },
    props: {
        showAddTask: Boolean,
    },
    data() {
        return {
            tasks: [],
        }
    },
    methods: {
        async toggleReminder(id) {
            const oldTask = await this.fetchTask(id)
            const updTask = { ...oldTask, reminder: !oldTask.reminder }

            const resp = await fetch(`api/tasks/${id}`, {
                method: 'PUT',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(updTask),
            })

            const data = await resp.json()

            this.tasks = this.tasks.map((task) => task.id === id ? { ...task, reminder: data.reminder } : task)
        },
        async addTask(task) {
            const resp = await fetch('api/tasks', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(task),
            })

            const data = await resp.json()

            this.tasks = [...this.tasks, data]
        },
        async deleteTask(id) {
            if (confirm('Are you sure?')) {
                const resp = await fetch(`api/tasks/${id}`, { method: 'DELETE' })

                resp.status === 200 ? this.tasks = this.tasks.filter((task) => task.id !== id)
                    : alter('Error deleting task')
            }
            console.log('task', id)
        },
        async fetchTasks() {
            const resp = await fetch('api/tasks')
            const data = resp.json()

            return data
        },
        async fetchTask(id) {
            const resp = await fetch(`api/tasks/${id}`)
            const data = resp.json()

            return data
        }
    },
    async created() {
        this.tasks = await this.fetchTasks()
    }
}
</script>