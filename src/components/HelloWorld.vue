<template>
  <div>
    <div class="input-wrapper"
      :error="inputError">
      <label for="">num of process: </label>
      <input type="text"
        placeholder=""
        v-model.number="listOfProgress">
    </div>
    <div class="input-wrapper">
      <label for="">num of maxium parallel: </label>
      <input type="number"
        v-model.number="numOfParallel">
    </div>
    <button @click="onClick">Confirm</button>
    <button @click="onStart">Start</button>
    <table>
      <colgroup>
        <col style="width: 70vw">
      </colgroup>
      <tr v-for="raw of listOfRows"
        :key="raw.id">
        <td>
          <Vprogress :maxiumAmount="raw.amount"
            :value="raw.value" />
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
import Vprogress from './progress-bar.vue'
import { ref, reactive, computed, watch } from 'vue'
import { mapLimit } from 'async'
import { interval } from 'rxjs'
import { take } from 'rxjs/operators'
import gsap from 'gsap'
export default {
  name: 'HelloWorld',
  components: {
    Vprogress,
  },
  setup() {
    const listOfProgress = ref(0)
    const numOfParallel = ref(3)
    const _list = reactive({ map: new Map() })
    const listOfRows = computed(() => [..._list.map].map(ele => ele[1]))
    const inputError = computed(() => /\d/g.test(listOfProgress.value))
    function onClick() {
      if (!inputError.value) return
      const list = Array.from({ length: listOfProgress.value }, (ele, i) => [
        i,
        {
          id: i,
          value: 0,
          amount: 100,
          time: ~~(Math.random() * 4 + 1),
          done: false,
        },
      ])
      _list.map = reactive(new Map(list))
    }
    function onStart() {
      mapLimit(
        listOfRows.value,
        numOfParallel.value,
        async ele => {
          console.log(ele.time)
          await gsap.to(ele, {
            duration: ele.time,
            value: ele.amount,
          })
          ele.done = true
        },
        () => {
          console.log('done')
        }
      )
    }

    return {
      listOfProgress,
      listOfRows,
      inputError,
      onClick,
      onStart,
      numOfParallel,
    }
  },
}
</script>
<style lang="stylus" scoped>
table
  width 100%
</style>