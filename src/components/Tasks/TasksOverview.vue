<template>
  <div class="container">
    <div class="flex-center flex-colum page-padding">
      <div v-for="item in items" :key="item.id" class="work">
        <TaskComponent
          :name="item.name"
          :id="item.id"
          :showCheck="true"
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
        <div class="flex-center">
          <ion-icon
            :icon="addCircleOutline"
            class="task-wizard__icon"
            @click="onEnter"
          />
          <ion-icon
            :icon="closeCircleOutline"
            class="task-wizard__icon task-wizard__icon--close"
            @click="openTaskWizard"
          />
        </div>
      </div>
      <button v-if="!renderWizard" @click="openTaskWizard()" class="add-btn">
        Add Task
      </button>

      <div v-for="item in finished" :key="item.id" class="finished">
        <TaskComponent
          :name="item.name"
          :id="item.id"
          :showCheck="false"
          @deleteItem="deleteFinishedTask"
          @checkItem="checkTask"
        ></TaskComponent>
      </div>
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
import { addCircleOutline, closeCircleOutline } from "ionicons/icons";
import TaskComponent from "./TaskComponent.vue";
import { IonIcon } from "@ionic/vue";
export default defineComponent({
  name: "TasksOverview",
  data() {
    return {
      taskInput: "",
      addCircleOutline,
      closeCircleOutline,
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
    deleteFinishedTask(id: number) {
      this.finished = this.finished.filter((item) => item.id != id);
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
  mounted() {
    this.loadTasks();
  },
  components: { TaskComponent, IonIcon },
});
</script>

<style lang="scss" scoped>
@import "../../theme/global.scss";

.container {
  height: 100%;
  display: flex;
  justify-content: center;
}
.task-wizard {
  display: flex;
  margin: 20px 0;
  width: 100%;
  min-width: 300px;
  &__input {
    width: 100%;
    height: 30px;
    background: transparent;
    border: 0;
    border-bottom: 1px solid var(--ion-color-success-shade);
    margin-right: 15px;
  }

  &__icon {
    font-size: 25px;
    &:hover {
      color: var(--ion-color-success-shade);
      cursor: pointer;
    }
    &--close {
      font-size: 25px;
      color: var(--ion-color-danger);
    }
  }
}
.work {
  &__task {
    margin-bottom: 8px;
    max-width: 350px;
  }
}
.finished {
  margin-top: 32px;
}

.add-btn {
  margin-top: 32px;
  border: solid 1px var(--ion-color-success-shade);
  border-radius: 25px;
  width: 100%;
  padding: 20px 50px 20px 50px;
  background: transparent;

  &:hover {
    background: var(--ion-color-success-shade);
  }
}
</style>
