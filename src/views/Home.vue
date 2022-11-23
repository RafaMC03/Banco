<template>
  <div>
    <Banner />
    <div class="main-container">
      <h1>Faça seu emprestimo:</h1>
      <Form />
    </div>
  </div>
</template>

<script>
import Banner from '../components/Banner'
import Form from '../components/Form'

export default {
  name: 'Home',
  components: {
    Banner,
    Form
  }
}
</script>