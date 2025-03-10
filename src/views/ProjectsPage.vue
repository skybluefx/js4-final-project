<template>
  <div>

    <h1>projects</h1>
    <t-modal
      :show="showDeleteModal"
      title="confirm delete"
      ok-button-label="delete"
      cancel-button-label="cancel"
      @close-me="closeDeleteModal"
      @ok-clicked="deleteProject"
      @cancel-clicked="closeDeleteModal"
    >
      <div>
        <span>Do you really want to delete project </span>
        <strong>{{ projectToDelete.project }}</strong>
        <span> ?</span>
      </div>
    </t-modal>
    <div class="large-btn-container">
      <t-button label="add project" @clicked="$router.push('/project-form')" />
    </div>

    <div v-if="!loading">
      <t-accordeon
        v-for="project in projectsToDisplay"
        :key="project.id"
        :title="project.project + ' (' + project.tasks.filter(task => task.completed).length + '/' + project.tasks.length + ')'"
      >
        <template v-slot:content>
          <div class="btn-container">
            <t-button label="detail" small-size @clicked="$router.push('/project/' + project.id)" />
            <t-button label="edit" small-size @clicked="$router.push('/project-form/' + project.id)" />
            <t-button
              v-if="!project.tasks.length"
              label="delete"
              small-size
              @clicked="onDeleteClicked(project)"
            />
            <t-button label="add task" small-size @clicked="$router.push('/task-form-project/' + project.id)" />
          </div>
          <t-list :items="project.tasks" display-icons />
        </template>
      </t-accordeon>
    </div>
  <t-loading v-else />
  </div>
</template>

<script>
// import { getProjects, getTasks } from '../data/data.js'
import db from '../helpers/db.js'
import { formatDate } from '../helpers/dateFunctions.js'
import TAccordeon from '../components/TAccordeon.vue'
import TList from '../components/TList.vue'
import TButton from '../components/TButton.vue'
import TLoading from '../components/TLoading.vue'
import TModal from '../components/TModal.vue'

export default {
  name: 'ProjectsPage',
  data () {
    return {
      projects: [],
      tasks: [],
      loading: true,
      showDeleteModal: false,
      projectToDelete: {}
    }
  },
  computed: {
    projectsToDisplay () { // [ {id: 1, project: 'zahrada', tasks: [{task1 ....}]}]
      return this.projects.map(project => {
        return Object.assign(
          {
            tasks: this.tasks.filter(task => project.id === task.projectid).map(item => {
              return {
                id: item.id,
                header: item.task,
                subtitle: formatDate(item.date),
                icon: item.completed ? 'check' : '',
                completed: item.completed
              }
            })
          },
          project
        )
      })
    }
  },
  created () {
    this.fetchData()
  },
  methods: {
    onDeleteClicked (project) { // { project: 'zahrada', id: 1, tasks: [] }
      this.projectToDelete = project
      this.showDeleteModal = true
    },
    deleteProject () {
      db.delete('js4projects', { id: this.projectToDelete.id }).then(() => {
        this.projectToDelete = {}
        this.showDeleteModal = false
        this.fetchData()
      })
    },
    closeDeleteModal () {
      this.projectToDelete = {}
      this.showDeleteModal = false
    },
    fetchData () {
      const promises = [
        db.get('js4projects').then(projects => {
          this.projects = projects
        }),
        db.get('js4tasks').then(tasks => {
          this.tasks = tasks
        })
      ]
      Promise.all(promises).then(() => {
        this.loading = false
      })
    }
  },
  components: { TAccordeon, TList, TButton, TLoading, TModal }
}

</script>
<style lang="stylus" scoped>
@import '../styles/variables.styl'
.btn-container
  display: flex
  justify-content: flex-end
  align-items: center
  padding: $list-items-padding
  border-bottom: 1px solid #efefef
.large-btn-container
  padding-bottom: 2rem
</style>
