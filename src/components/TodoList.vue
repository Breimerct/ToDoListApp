<template>
  <q-list separator>
    <q-item v-for="(task, index) in tasks" :key="task.id">
      <q-item-section class="cursor-pointer">
        <p class="text-justify text-wrap">
          {{ task.title }}
        </p>
        <q-tooltip
          transition-show="rotate"
          transition-hide="rotate"
          anchor="top middle" self="top middle"
        >
          {{ i18n.global.t('LABELS.EDIT_TASK') }}
        </q-tooltip>
        <q-popup-edit
          v-model="task.title"
          buttons
          :label-set="i18n.global.t('BUTTONS.SAVE')"
          :label-cancel="i18n.global.t('BUTTONS.CANCEL')"
          :validate="validateTask"
          @save="saveUpdated($event, task)"
          @cancel="clearErrors"
          @close="clearErrors"
          v-slot="scope"
          :model-value="task.title"
        >
          <q-input
            v-model="scope.value"
            dense
            :error="titleValidate"
            :error-message="validateMessage"
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
import {defineComponent, inject, ref} from 'vue'
import { i18n } from "boot/i18n";

export default defineComponent({
  name: "TodoList",

  setup (props, context) {
    const tasks = ref([])
    const titleValidate = ref(false)
    const validateMessage = ref('')

    tasks.value = inject('tasks')

    const saveUpdated = (value, task) => {
      context.emit('onEdit', {
        task: task,
        titleEdited: value
      })
    }

    const validateTask = (val) => {
      if (val.length !== 0) {
        titleValidate.value = false
        return true
      } else {
        titleValidate.value = true
        validateMessage.value = i18n.global.t('OBLIGATORY_FIELD')
        return false
      }
    }

    const  clearErrors = () => {
      validateMessage.value = ''
      titleValidate.value = false
    }

    return {
      tasks,
      titleValidate,
      validateMessage,
      i18n,
      saveUpdated,
      validateTask,
      clearErrors
    }
  }
})
</script>

<style lang="scss" scoped>
.text-wrap {
  word-break: break-word;
}
</style>
