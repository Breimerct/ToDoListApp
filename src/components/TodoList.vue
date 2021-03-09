<template>
  <q-list separator>
    <q-item v-for="(task, index) in tasks" :key="task.id">
      <q-item-section class="cursor-pointer">
        <p class="text-justify text-wrap">
          {{ task.title }}
        </p>
        <q-popup-edit
          v-model="task.title"
          buttons label-set="save"
          label-cancel="cancel"
          @save="saveUpdated($event, task)"
          v-slot="scope"
          :model-value="task.title"
        >
          <q-input
            v-model="scope.value"
            dense
            autofocus
            counter
            @keyup.enter="scope.set"
            :model-value="scope.value"
          />
        </q-popup-edit>
      </q-item-section>
      <q-item-section avatar>
        <q-btn
          dense
          flat
          rounded
          color="negative"
          icon="eva-trash-outline"
          @click="$emit('onDelete', index)"
        />
      </q-item-section>
    </q-item>
  </q-list>
</template>

<script>
import {defineComponent, inject, onMounted, ref} from 'vue'
export default defineComponent({
  name: "TodoList",

  setup (props, context) {
    const tasks = ref([])

    tasks.value = inject('tasks')

    const saveUpdated = (value, task) => {
      context.emit('onEdit', {
        task: task,
        titleEdited: value
      })
    }

    return {
      tasks,
      saveUpdated
    }
  }
})
</script>

<style lang="scss" scoped>
.text-wrap {
  word-break: break-word;
}
</style>
