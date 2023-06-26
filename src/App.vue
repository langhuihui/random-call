<script setup lang="ts">
import Queue from './components/Queue.vue';
import Block from './components/Block.vue';
import { nextTick, reactive, ref } from 'vue';
const speed = ref(0.1);
class Task {
  progress = 0;
  aborting = 0;
  opacity = 0;
  constructor(public type = 0) {
  }
  forward() {
    if (this.progress < 100) {
      this.progress += speed.value;
      return false;
    }
    return true;
  }
  abort() {
    this.aborting = Date.now();
  }
}
const queue = reactive<Task[]>([]);
const _queue: Task[] = [];
const move = () => {
  if (queue.length) {
    if (!queue[0].aborting) {
      const done = queue[0].forward();
      if (done) {
        queue.shift();
        _queue.shift();
      }
    }
    queue.forEach(t => {
      if (t.aborting > 0) {
        t.opacity = (500 - Date.now() + t.aborting) / 500;
        if (t.opacity <= 0)
          queue.splice(queue.indexOf(t), 1);
      }
    });
  }
  requestAnimationFrame(move);
};
move();
function add(type: number) {
  const nt = new Task(type);
  if (_queue.length == 1) {
    switch (_queue[0].type) {
      case 0:
      case 1:
        if (type == 0) {
          nt.abort();
        }
        break;
      case 2:
        if (type != 0) {
          nt.abort();
        }
    }
  } else if (_queue.length > 1) {
    switch (type) {
      case 2:
        _queue.forEach((t, i) => i && t.abort());
        _queue.length = 1;
        if (_queue[0].type == 2) nt.abort();
        break;
      case 1:
        if (_queue[_queue.length - 1].type == 1) {
          nt.abort();
        } else if (_queue[_queue.length - 1].type == 2) {
          nt.abort();
        }
        break;
      case 0:
        switch (_queue[_queue.length - 1].type) {
          case 1:
            nt.abort();
            break;
          case 0:
            _queue.pop()!.abort();
            break;
          case 2:
            _queue.pop()!.abort();
            nt.abort();
            break;
        }
    }
  }
  if (!nt.aborting) _queue.push(nt);
  queue.push(nt);
}
</script>

<template>
  <Queue>
    <Block v-for="item in queue" v-bind="item">

    </Block>
  </Queue>
  <div class="buttons">
    <Block :type="0" @click="add(0)"></Block>
    <Block :type="1" @click="add(1)"></Block>
    <Block :type="2" @click="add(2)"></Block>
    <button @click="speed += 0.1">speed up</button>
    <button @click="speed > 0 ? speed -= 0.1 : void 0">speed down</button>
  </div>
</template>

<style>
body {
  background-color: gray;
}

button {
  margin: 5px;
}

.buttons {
  display: flex;
  position: fixed;
  bottom: 10px;
  right: 0;
}
</style>