<template>
  <div class="todo-header">
    <input
      type="text"
      placeholder="请输入你的任务名称，按回车键确认"
      v-model="taskName"
      @keydown.enter="sendTaskName"
    />
  </div>
</template>

<script lang='ts'>
import { defineComponent, Ref, ref } from "vue";
import bus from "../utils";
import { nanoid } from 'nanoid'
export default defineComponent({
  name: "Header",
  setup() {
    const taskName:Ref = ref("");
    /* 添加任务 */
    function sendTaskName () {
        let id=nanoid(10);
        bus.emit('sendMsg',{id:id,name:taskName.value,isChecked:false})
    }
    return {
      taskName,
      sendTaskName,
    }
  },
});
</script>

<style scoped>
/*header*/
.todo-header input {
  width: 560px;
  height: 28px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 4px 7px;
}

.todo-header input:focus {
  outline: none;
  border-color: rgba(82, 168, 236, 0.8);
  box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075),
    0 0 8px rgba(82, 168, 236, 0.6);
}
</style>