<template>
  <q-page class="bg-grey-3 column">
    <div class="row q-pa-sm bg-primary">
      <q-input
        v-model="newTask"
        @keyup.enter="addTask"
        class="col"
        square
        filled
        bg-color="white"
        placeholder="Add Task"
        maxlength="12"
        dense>
        <template v-slot:append>
          <q-btn
            @click="addTask"
            round
            dense
            flat
            icon="add" />
        </template>
      </q-input>
    </div>
    <q-list
    class="bg-white"
    seperator
    bordered>


      <q-item
      v-for ="(task, index) in tasks"
      :key="task.title"
      @click="task.done = !task.done"
      :class="{'done bg-blue-1' : task.done }"
      clickable
      v-ripple>
        <q-item-section avatar>
          <q-checkbox
            v-model="task.done"
            class="no-pointer-events"
            color="primary" />
        </q-item-section>
        <q-item-section>
          <q-item-label>{{ task.title }}</q-item-label>
        </q-item-section>
        <q-item-section
          v-if="task.done"
          side>
          <q-btn
            @click.stop="deleteTask(index)"
            flat
            round
            dense
            color="primary"
            icon="delete" />
        </q-item-section>
      </q-item>


    </q-list>
    <div
    v-if="!tasks.length"
    class="no-tasks absolute-center">
      <q-icon
        name="check"
        size="100px"
        color="primary"/>
      <div class="text-h5 text-primary text-center">
        No Tasks
      </div>
    </div>
  </q-page>
</template>

<script>
import { data } from 'autoprefixer';
import { defineComponent } from 'vue'
import { ref } from 'vue'

export default defineComponent({
  name: 'TodoPage',
  data() {
    return {
      newTask: '',
      tasks: []

    }
  },
  methods: {
      deleteTask(index) {
      this.$q.dialog({
        title: 'Confirm',
        message: 'Really Delete?',
        cancel: true,
        persistent: true
      }).onOk(() => {
        this.tasks.splice(index, 1);
        this.saveTasks();
        this.$q.notify('Task Deleted')
      })
    },
    addTask() {
      if (this.newTask.trim().length) {
      this.tasks.push({
        title: this.newTask,
        done: false
      })
      this.newTask = '';
      this.saveTasks();
      }
    },
    saveTasks() {
    localStorage.setItem('tasks', JSON.stringify(this.tasks))
    },
    loadTasks() {
      const tasks = localStorage.getItem('tasks')
      if (tasks) {
        try {
        this.tasks = JSON.parse(tasks);
        } catch(e) {
          console.error("Failed to parse tasks:", e)
        }
      }
    }
  },
  mounted() {
    this.loadTasks();
  }
})


</script>

<style lang="scss">
  .done {
    .q-item__label {
      text-decoration: line-through;
      color: #bbb;
    }
  }

  .no-tasks {
    opacity: 0.5;
  }
</style>


