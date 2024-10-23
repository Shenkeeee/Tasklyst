<template>
    <div
        class="add-task-panel flex justify-center gap-5 items-center pt-5 flex-wrap w-full bg-gray-200/15 p-4 rounded shadow-md">
        <select v-model="selectedStatus" class="mr-2 p-2 border border-gray-300 rounded">
            <option v-for="status in Object.keys(columns)" :key="status" :value="status">{{ status }}</option>
        </select>
        <input v-model="newTaskName" @keyup.enter="addTask" placeholder="New task"
            class="mr-2 p-2 border border-gray-300 rounded" />
        <button @click="addTask" :class="buttonClass">Add Task</button>
    </div>
    <div class="board-container flex flex-wrap justify-center gap-6 items-start p-5 box-border w-full">
        <div class="column min-w-60 sm:w-80 p-5 border border-teal-50 rounded min-h-[50vh]  box-border shadow-lg shadow-gray-500/20"
            :class="columnClass(status)" v-for="(tasks, status) in columns" :key="status">
            <h2 class="column-title text-2xl font-bold mb-2 text-center">{{ status }}</h2>
            <draggable v-model="columns[status]" group="tasks" @start="drag = true" @end="onDragEnd" item-key="id">
                <template #item="{ element }">
                    <div
                        class="task-card p-2 mb-2 border border-gray-200 rounded bg-white shadow flex justify-between items-center *:m-2">
                        <input v-if="element.editing" v-model="element.name" @blur="element.editing = false"
                            @keyup.enter="element.editing = false" class="flex-grow mr-2" />
                        <span v-else @dblclick="editTask(element)">{{ element.name }}</span>
                        <button @click="removeTask(status, element.id)"
                            class="bg-red-500 text-white rounded px-2 py-1 hover:bg-red-700">Delete</button>
                    </div>
                </template>
            </draggable>
        </div>
    </div>
</template>

<script>
import { ref, computed } from 'vue';
import draggable from 'vuedraggable';

export default {
    components: {
        draggable,
    },
    setup() {
        const drag = ref(false);
        const columns = ref({
            'To Do': [
                { id: 1, name: 'Set up Laravel project', editing: false },
                { id: 2, name: 'Create database schema', editing: false },
                { id: 4, name: 'Set up unit testing', editing: false },
                { id: 5, name: 'Deploy application', editing: false }
            ],
            'In Progress': [
                { id: 6, name: 'Develop API endpoints', editing: false },
                { id: 7, name: 'Implement authentication', editing: false },
                { id: 3, name: 'Add task due dates', editing: false },
            ],
            'Done': [
                { id: 8, name: 'Initialize Vue project', editing: false },
                { id: 9, name: 'Create user profile page', editing: false },
                { id: 11, name: 'Create task components', editing: false },
                { id: 10, name: 'Implement task filtering', editing: false },
                { id: 12, name: 'Set up Vue Router', editing: false },
            ],
        });

        const newTaskName = ref('');
        const selectedStatus = ref('To Do');

        const COLORS = {
            TODO: 'bg-yellow-100',
            IN_PROGRESS: 'bg-amber-200',
            DONE: 'bg-green-400'
        };

        const onDragEnd = () => {
            drag.value = false;
            console.log('Drag ended');
        };

        const addTask = () => {
            if (newTaskName.value.trim() !== '') {
                const newId = Date.now();
                columns.value[selectedStatus.value].push({ id: newId, name: newTaskName.value, editing: false });
                newTaskName.value = '';
            }
        };

        const removeTask = (status, taskId) => {
            columns.value[status] = columns.value[status].filter(task => task.id !== taskId);
        };

        const editTask = (task) => {
            task.editing = true;
        };

        const buttonClass = computed(() => {
            const baseClass = 'rounded px-4 py-2';
            const statusClasses = {
            'To Do': `${COLORS.TODO} hover:bg-yellow-200`,
            'In Progress': `${COLORS.IN_PROGRESS} hover:bg-amber-300`,
            'Done': `${COLORS.DONE} hover:bg-green-500`,
            'default': 'bg-trueGray-50 hover:bg-trueGray-200'
            };
            return `${baseClass} ${statusClasses[selectedStatus.value] || statusClasses['default']}`;
        });

        const columnClass = (status) => {
            return {
                'To Do': COLORS.TODO,
                'In Progress': COLORS.IN_PROGRESS,
                'Done': COLORS.DONE
            }[status] || '';
        };

        return {
            drag,
            columns,
            newTaskName,
            selectedStatus,
            onDragEnd,
            addTask,
            removeTask,
            editTask,
            buttonClass,
            columnClass,
        };
    },
};
</script>
