<template>
  <div class="flex-center flex-colum page-padding">
    <div v-for="item in items" :key="item.id" class="work">
      <TaskComponent
        :name="item.name"
        :id="item.id"
        class="work__task"
        @deleteItem="deleteTask"
        @checkItem="checkTask"
      ></TaskComponent>
    </div>
    <div v-if="renderWizard" class="flex-center task-wizard">
      <input
        class="task-wizard__input"
        type="text"
        placeholder="What are you working on?"
        v-on:keydown.enter="onEnter"
        v-model="taskInput"
      />
      <ion-icon
        :icon="addCircleOutline"
        class="task-wizard__icon"
        @click="onEnter"
      />
    </div>
    <button @click="openTaskWizard()" class="add-btn">Add Task</button>

    <div v-for="item in finished" :key="item.id" class="finished">
      <TaskComponent
        :name="item.name"
        :id="item.id"
        @deleteItem="deleteTask"
        @checkItem="checkTask"
      ></TaskComponent>
    </div>
  </div>
</template>

<script lang="ts">
export type TaskType = {
  id: number;
  name: string;
  rounds: number;
};

import { defineComponent } from "vue";
import { addCircleOutline } from "ionicons/icons";
import TaskComponent from "./TaskComponent.vue";
import { IonIcon } from "@ionic/vue";
export default defineComponent({
  name: "TasksOverview",
  data() {
    return {
      taskInput: "",
      addCircleOutline,
      renderWizard: false,
      items: [] as Array<TaskType>,
      finished: [] as Array<TaskType>,
    };
  },
  methods: {
    openTaskWizard() {
      this.renderWizard = !this.renderWizard;
    },
    onEnter() {
      if (this.taskInput.length) {
        this.addItem(this.taskInput);
        this.taskInput = "";
      }
    },
    // item stuff
    addItem(name: string) {
      this.items.push({ id: Date.now(), name, rounds: 0 });
      this.saveTasks();
    },
    deleteTask(id: number) {
      this.items = this.items.filter((item) => item.id != id);
      this.saveTasks();
    },
    checkTask(id: number) {
      const item = this.items.find((item) => item.id === id);
      if (item) {
        this.finished.push(item);
      }
      this.deleteTask(id);
      this.saveTasks();
    },

    // save / load
    saveTasks() {
      localStorage.setItem("tasks", JSON.stringify(this.items));
    },

    loadTasks() {
      const storage = localStorage.getItem("tasks");
      if (storage) {
        this.items = JSON.parse(storage);
      }
    },
  },
  components: { TaskComponent, IonIcon },
});
</script>

<style lang="scss">
@import "../../theme/global.scss";
.task-wizard {
  display: flex;
  margin: 20px;
  &__input {
    width: 100%;
    background: transparent;
    border-radius: 5px;
  }
  &__icon {
    font-size: 25px;
    &:hover {
      color: var(--ion-color-success-shade);
      cursor: pointer;
    }
  }
}
.work {
  &__task {
    margin-bottom: 8px;
  }
}
.finished {
  margin-top: 32px;
}

.add-btn {
  margin-top: 32px;
  border: solid 1px var(--ion-color-success-shade);
  border-radius: 25px;
  padding: 20px 50px 20px 50px;
  background: transparent;
}
</style>
