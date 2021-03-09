<template>
  <q-page class="fit row justify-center q-px-lg non-selectable" :class="{ 'bg-grey-8' : isDark }">
    <div class="col-md-8 col-12">
      <q-card class="q-mt-xl shadow-10">
        <q-btn
          flat
          dense
          rounded
          class="q-ma-sm float-right btn-dark"
          :icon="isDark ? 'eva-sun' : 'eva-moon-outline'"
          @click="$q.dark.toggle()"
        />
        <q-card-section class="text-center">
          <div class="text-h4">
            ToDo List App
          </div>
        </q-card-section>
        <q-card-section>
          <q-form @submi.preventt="saveTask">
            <q-input
              filled
              bottom-slots
              v-model="title"
              label="Name Task"
              counter
              :color="isDark ? 'white' : 'black'"
              :model-value="title"
              ref="inputTask"
            >
              <template v-slot:append>
                <q-icon v-if="title !== ''" name="close" @click="title = ''" class="cursor-pointer" />
              </template>

              <template v-slot:hint v-if="title.length === 120">
                Field hint
              </template>

              <template v-slot:after>
                <q-btn round flat :color="isDark ? 'white' : 'black'" icon="eva-save-outline" type="submit" @click="saveTask">
                  <q-tooltip :offset="[-75, -75]">
                    Save task
                  </q-tooltip>
                </q-btn>
              </template>
            </q-input>
          </q-form>
        </q-card-section>
        <q-separator :color="isDark ? 'white' : ''" v-if="tasks.length > 0"/>
        <q-card-section v-if="tasks.length > 0">
          <todo-list @onDelete="deleteTask" @onEdit="editTask"/>
        </q-card-section>
      </q-card>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref, provide, computed } from 'vue';
import { useQuasar } from 'quasar';
import TodoList from "components/TodoList";

export default defineComponent({
  name: 'PageIndex',
  components: {TodoList},
  setup () {
    const $q = new useQuasar()
    const title = ref('')
    const tasks = ref([])
    const inputTask = ref(null)

    const isDark = computed(() => {
      return $q.dark.isActive
    })

    const saveTask = () => {
      if (title.value) {
        tasks.value.push({
          id: Date.now(),
          title: title.value,
          status: false
        })
        title.value = ''
        inputTask.value.focus()

      } else {
        $q.dialog({
          title: 'Warning!',
          message: 'enter the task name',
          class: ['bg-warning', 'text-white', 'text-center'],
          ok: {
            push: true,
            color: 'secondary'
          }
        })
      }
    }

    const deleteTask = (e) => {
      tasks.value.splice(e, 1)
    }

    const editTask = (e) => {
      const taskEdit = tasks.value.filter(t => t.id === e.task.id)
      taskEdit[0].title = e.titleEdited
      console.log(tasks.value)
    }

    provide('tasks', tasks.value)

    return {
      title,
      isDark,
      tasks,
      inputTask,
      saveTask,
      deleteTask,
      editTask
    }
  }
})
</script>

<style lang="scss" scoped>
.btn-dark {
  z-index: 1;
}
</style>
