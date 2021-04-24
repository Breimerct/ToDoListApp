<template>
  <q-page class="fit row justify-center q-px-lg non-selectable" :class="{ 'bg-grey-8' : isDark }">
    <div class="col-md-8 col-12">
      <q-card class="q-mt-xl shadow-10">
        <q-avatar
          size="md"
          text-color="white"
          class="float-left q-ma-sm btn-dark cursor-pointer"
          @click="changeLang"
        >
          <img src="../assets/flag-eeuu.png" alt="flag-eeuu" v-if="i18n.global.locale === 'es'"/>
          <img src="../assets/flag-col.png" alt="flag-col" v-else/>
        </q-avatar>
        <q-btn
          flat
          dense
          rounded
          class="q-ma-sm float-right btn-dark"
          :icon="isDark ? 'eva-sun' : 'eva-moon-outline'"
          @click="darkToggle"
        />
        <q-card-section class="text-center">
          <div class="text-h4">
            ToDo List App
          </div>
        </q-card-section>
        <q-card-section>
          <q-form>
            <q-input
              :dark="isDark"
              filled
              bottom-slots
              v-model="title"
              :label="i18n.global.t('LABELS.NAME_TASK')"
              counter
              :color="isDark ? 'white' : 'black'"
              :model-value="title"
              ref="inputTask"
              :error="errorInput"
              :error-message="messageError"
              @keyup="validateInput"
            >
              <template v-slot:append>
                <q-icon v-if="title !== ''" name="close" @click="title = ''" class="cursor-pointer" />
              </template>

              <template v-slot:after>
                <q-btn round flat :color="isDark ? 'white' : 'black'" icon="eva-save-outline" type="submit" @click="saveTask">
                  <q-tooltip :offset="[-75, -75]">
                    {{ i18n.global.t('LABELS.SAVE_TASK') }}
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
import {computed, defineComponent, onMounted, provide, ref} from 'vue';
import {useQuasar} from 'quasar';
import {i18n} from "boot/i18n";
import TodoList from "components/TodoList";

export default defineComponent({
  name: 'PageIndex',
  components: {
    TodoList

  },
  setup () {
    const $q = new useQuasar()
    const title = ref('')
    const tasks = ref([])
    const inputTask = ref(null)
    const messageError = ref('')
    const errorInput = ref(false)

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
        localStorage.setItem('tasks', JSON.stringify(tasks.value))
        title.value = ''
        errorInput.value = false
        messageError.value = ''
        inputTask.value.focus()
      } else {
        errorInput.value = true
        messageError.value = i18n.global.t('OBLIGATORY_FIELD')
      }
    }

    const deleteTask = (e) => {
      $q.dialog({
        title: i18n.global.t('TITLES.CONFIRM'),
        message: i18n.global.t('QUESTION_DELETE_TASK'),
        ok: {
          label: i18n.global.t('LABELS.YES'),
          show: true,
          color: 'primary',
          outline: true
        },
        cancel: {
          label: i18n.global.t('LABELS.NO'),
          show: true,
          color: 'primary'
        }
      }).onOk(() => {
        tasks.value.splice(e, 1)
        localStorage.setItem('tasks', JSON.stringify(tasks.value))
      })
    }

    const editTask = (e) => {
      if (e.titleEdited) {
        const taskEdit = tasks.value.filter(t => t.id === e.task.id)
        taskEdit[0].title = e.titleEdited
        localStorage.setItem('tasks', JSON.stringify(tasks.value))
      } else {
        $q.dialog({
          title: 'Warning!',
          message: i18n.global.t('INSERT_TASK_NAME'),
          class: ['bg-warning', 'text-white', 'text-center'],
          ok: {
            push: true,
            color: 'secondary'
          }
        })
      }
    }

    const validateInput = (val) => {
      if (val.length <= 0) {
        errorInput.value = true
        messageError.value = i18n.global.t('OBLIGATORY_FIELD')
      } else {
        errorInput.value = false
        messageError.value = ''
      }
    }

    const changeLang = () => {
      if (i18n.global.locale === 'es') {
        i18n.global.locale = 'en'
      } else {
        i18n.global.locale = 'es'
      }
    }

    onMounted(() => {
      if (localStorage.getItem('isDark')) {
        const dark = JSON.parse(localStorage.getItem('isDark'))
        $q.dark.set(dark.value)
      }
      inputTask.value.focus()
      if (localStorage.getItem('tasks') !== null) {
        Object.values(JSON.parse(localStorage.getItem('tasks')))
          .forEach(val => {
            tasks.value.push(val)
          })
      }
    })

    const darkToggle = () => {
      $q.dark.toggle()
      const dark = {
        value: $q.dark.isActive
      }
      localStorage.setItem('isDark', JSON.stringify(dark))
    }

    provide('tasks', tasks.value)

    return {
      title,
      isDark,
      tasks,
      inputTask,
      errorInput,
      messageError,
      i18n,
      saveTask,
      deleteTask,
      editTask,
      validateInput,
      changeLang,
      darkToggle
    }
  }
})
</script>

<style lang="scss" scoped>
.btn-dark {
  z-index: 1;
}
</style>
