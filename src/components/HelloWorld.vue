<template>
  <div>
    <div class="input-wrapper"
      :error="inputError">
      <input type="text"
        placeholder=""
        v-model="listOfProgress">
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
export default {
  name: 'HelloWorld',
  components: {
    Vprogress
  },
  setup() {
    const listOfProgress = ref('')
    const _list = reactive({ map: new Map() })
    const listOfRows = computed(() => [..._list.map].map(ele => ele[1]))
    const inputError = computed(() => /(\d|\.)g/.test(listOfProgress.value))
    watch([listOfProgress, _list], () => {
      console.log(listOfRows.value)
    })
    function onClick() {
      if (inputError.value) return
      const list = listOfProgress.value.split('.').map((ele, i) => [
        i,
        {
          id: i,
          value: 0,
          amount: +ele,
          done: false
        }
      ])
      _list.map = reactive(new Map(list))
    }
    function onStart() {
      return new Promise(resolve => {
        mapLimit(
          listOfRows.value,
          4,
          ele => {
            return new Promise(res => {
              console.log(ele.value)
              console.log(ele)
              interval(1000)
                .pipe(take(ele.amount))
                .subscribe({
                  next: e => {
                    console.log(e)
                    ele.value = e + 1
                  },
                  complete() {
                    ele.done = true
                    res()
                  }
                })
            })
          },
          () => {
            resolve()
          }
        )
      })
    }

    return {
      listOfProgress,
      listOfRows,
      inputError,
      onClick,
      onStart
    }
  }
}
</script>
