<template>
    <div>
        <b-nav tabs>
        <b-nav-item disabled><b>Celebrity 10 Quiz App</b></b-nav-item>
        <b-nav-item disabled>Correct Answer Counter: {{ numCorrect }}/{{ numTotal }}</b-nav-item>
    </b-nav>
    </div>
</template>
<script>
export default {
  props:
  [
    'numCorrect',
    'numTotal'
  ]
}
</script>
