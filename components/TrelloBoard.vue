
<script setup lang="ts">
import { ref } from 'vue'
import { nanoid } from 'nanoid'
import draggable from 'vuedraggable'
import type { Column, Task } from '../types';

const columns = ref<Column[]>([
    {
        id: nanoid(),
        title: 'Backlog',
        tasks: [
        { id: nanoid(), title: 'Create marketing landing page', createdAt: new Date() },
        { id: nanoid(), title: 'Develop cool new feature', createdAt: new Date() },
        { id: nanoid(), title: 'Fix page nav bug', createdAt: new Date() }
        ]
    },
    {
        id: nanoid(),
        title: 'Selected for Dev',
        tasks: []
    },
    {
        id: nanoid(),
        title: 'In progress',
        tasks: []
    },
    {
        id: nanoid(),
        title: 'QA',
        tasks: []
    },
    {
        id: nanoid(),
        title: 'Completed',
        tasks: []
    },       
])

const alt = useKeyModifier("Alt")

function createColumn() {
  const column: Column = {
    id: nanoid(),
    title: '',
    tasks: [],
  }
  columns.value.push(column)
  
  nextTick(() => {
        (document.querySelector('.column:last-of-type .title-input') as HTMLInputElement).focus()
    })
}

</script>

<template>
    <div class="flex items-start overflow-x-auto gap-4">
        <draggable
            v-model="columns"
            group="columns"
            item-key="id"
            :animation="150"
            handle=".dra-handle"
            class="flex gap-4 items-start">
            <template #item="{element: column} : {element : Column}">
                <div class="column bg-gray-200 p-5 rounded min-w-[250px]">        
                    <header class="font-bold mb-4">
                       <DragHandle/>
                       <input class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5"
                        @keyup.enter="($event.target as HTMLInputElement).blur()"
                        @keydown.backspace="column.title === '' ? columns = columns.filter(col => col.id !== column.id) : null"
                        type="text" v-model="column.title" />
                    </header>
                    <draggable
                        v-model="column.tasks"
                        :group="{ name: 'tasks', pull: alt ? 'clone' : true }"
                        item-key="id"
                        :animation="150"                                              
                        >
                        <template #item="{element: task} : {element : Task}">
                            <div>
                                <TrelloBoardTask  :task="task"  @delete="column.tasks = column.tasks.filter(item => item.id !== $event)" />
                            </div>                            
                        </template>
                       
                    </draggable>
                    
                    <footer>
                        <NewTask @add="column.tasks.push($event)" />
                    </footer>
                </div>
            </template>    
        </draggable> 
        <button @click="createColumn" class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50">
            + Add another column
         </button>      
    </div>
</template>



<style lang="scss" scoped>

</style>