<template>
  <div class="modal-card">
    <header class="modal-card-head">
      <p class="modal-card-title">Ask a question</p>
      <button class="delete" aria-label="close" @click="$parent.close()"></button>
    </header>
    <section class="modal-card-body">
      <div class="field">
        <label class="label">Text</label>
        <div class="control">
          <textarea class="textarea" required v-model="question.text" />
        </div>
        <p class="help is-danger" v-if="errors.text">{{ errors.text.join(', ')}}</p>
      </div>
    </section>
    <footer class="modal-card-foot">
      <button class="button is-primary" @click="saveQuestion()">Save changes</button>
      <button class="button" @click="$parent.close()">Cancel</button>
    </footer>
  </div>
</template>

<script>
export default {
  name: 'EditQuestionModal',
  props: ['event', 'questionLine', 'question', 'services', 'showSuccess', 'showDanger', 'router'],
  data () {
    return {
      errors: {}
    }
  },
  computed: {
    isNew () {
      return !this.question.id
    }
  },
  methods: {
    saveQuestion () {
      this.isLoading = true

      const action = this.isNew
        ? this.axios.post(this.services['oms-statutory'] + '/events/' + this.event.id + '/question-lines/' +  this.questionLine.id + '/questions/', this.question)
        : this.axios.put(this.services['oms-statutory'] + '/events/' + this.event.id + '/question-lines/' + this.questionLine.id + '/questions/' + this.question.id, this.question)

      action.then((response) => {
        this.showSuccess('Question is created.')

        this.isLoading = false
        this.router.go(0) // Reloading the page.
      }).catch((err) => {
        this.isLoading = false
        let message = err.response && err.response.status === 422 ? 'Some fields were not set: ' : err.message
        if (err.response && err.response.data && err.response.data.errors) this.errors = err.response.data.errors

        this.showDanger(message)
      })
    }
  },
  mounted () {
    
  }
}
</script>