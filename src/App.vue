<template>
  <div class="bg"></div>
  <div class="box">
    <h3>第 {{level}} 关 </h3>
    <div class="app">
      <div class="scene-container">
        <div class="scene-inner">
          <item v-for="(item,index) in scene" :key="item.id" :isCover="item.isCover" :icon="item.icon"
            :status="item.status" :x=" item.status===0 ? item.x : item.status===1 ? sortedQueue[item.id] : -1000 "
            :y="item.status===0 ? item.y : 895" @click="clickSymbol(index)" />
        </div>
      </div>
    </div>
    <div class="queue-container flex-container flex-center"></div>
    <div class="flex-container flex-between">
      <button class="flex-grow" @click="pop">
        弹出
      </button>
      <button class="flex-grow" @click="undo">
        撤销
      </button>
      <button class="flex-grow" @click="wash">
        洗牌
      </button>
      <button class="flex-grow" @click="levelUp">
        下一关
      </button>
    </div>
    <div class="modal" v-if="finished">
      <h1>{{tipText}}</h1>
      <button @click="restart">再来一次</button>
    </div>
  </div>

</template>

<script setup lang="ts">
const icons = [
  'data:image/gif;base64,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

QmCC];

import item from './components/item.vue'
import { ref, watchEffect } from 'vue'
import { randomString, waitTimeout } from './utils';
// 初始icon数组
/*const icons = [icon1, icon2, icon3, icon4, icon5, icon6, icon7, icon8, icon9, icon10, icon11, icon12, icon13, icon14, icon15, icon16, icon17, icon18, icon19];*/
// 设置最大随机关卡
const maxLevel = 50;
// 以下感谢大佬们提供的算法
const makeScene = (level) => {
  // 获取当前关卡
  const curLevel = Math.min(maxLevel, level);
  // 获取当前关卡应该拥有的icon数量
  const iconPool = icons.slice(0, 2 * curLevel);
  // 算出偏移量范围具体细节范围
  const offsetPool = [0, 25, -25, 50, -50].slice(0, 1 + curLevel);
  //  最终的元数据数组
  const scene = [];
  // 确定范围
  //在一般情下 translate 的偏移量，如果是百分比的话，是按照自身的宽度或者高度去计算的，所以最大的偏移范围是百分800%
  // 然后通过Math.random 会小于百分之八百
  // 所以就会形成当前区间的随机数
  const range = [
    [2, 6],
    [1, 6],
    [1, 7],
    [0, 7],
    [0, 8],
  ][Math.min(4, curLevel - 1)];
  const randomSet = (icon: string) => {
    // 求偏移量
    const offset = offsetPool[Math.floor(offsetPool.length * Math.random())];
    // 偏移求列数
    const row = range[0] + Math.floor((range[1] - range[0]) * Math.random());
    // 求偏移行数
    const column = range[0] + Math.floor((range[1] - range[0]) * Math.random());
    console.log(offset, row, column);
    // 生成元数据对象
    scene.push({
      isCover: false, // 默认都是没有被覆盖的
      status: 0,// 是否被选中的状态
      icon,// 图标
      id: randomString(4), // 生成随机id
      x: column * 100 + offset, //x 坐标
      y: row * 100 + offset,// y坐标
    });
  };

  // 如果级别高了就加点icon 花哨一点
  let compareLevel = curLevel;
  while (compareLevel > 0) {
    iconPool.push(...iconPool.slice(0, Math.min(10, 2 * (compareLevel - 5))));
    compareLevel -= 5;
  }
  // 生成元数据，初始状态下 iconPool的内容少生 随着增加，就会越来越难
  for (const icon of iconPool) {
    for (let i = 0; i < 6; i++) {
      randomSet(icon);
    }
  }

  return scene;
};
// 洗牌
const washScene = (level, scene) => {
  // 洗牌的随机逻辑与初始化相同
  const updateScene = scene.slice().sort(() => Math.random() - 0.5);
  const offsetPool = [0, 25, -25, 50, -50].slice(0, 1 + level);
  const range = [
    [2, 6],
    [1, 6],
    [1, 7],
    [0, 7],
    [0, 8],
  ][Math.min(4, level - 1)];

  const randomSet = (symbol) => {
    const offset = offsetPool[Math.floor(offsetPool.length * Math.random())];
    const row = range[0] + Math.floor((range[1] - range[0]) * Math.random());
    const column = range[0] + Math.floor((range[1] - range[0]) * Math.random());
    symbol.x = column * 100 + offset;
    symbol.y = row * 100 + offset;
    symbol.isCover = false;
  };
  //遍历初始化数组
  for (const symbol of updateScene) {
    // 碰见已经选中的不处理
    if (symbol.status !== 0) continue;
    randomSet(symbol);
  }

};
const scene = ref(makeScene(1)) // 元数据
const level = ref(1); // 等级
const queue: any = ref([]);// 下方选中数据
const sortedQueue = ref({});// 排序内容
const finished = ref(false);// 是否完成
const tipText = ref('');// 提示
const animating = ref(false);// 动画
// 检查是否被覆盖
const checkCover = (value) => {
  // 深拷贝一份
  const updateScene = value.slice();
  // 是否覆盖算法
  // 遍历所有的元数据
  // 双重for循环来找到每个元素的覆盖情况
  for (let i = 0; i < updateScene.length; i++) {
    // 当前item对角坐标
    const cur = updateScene[i];
    // 先假设他都不是覆盖的
    cur.isCover = false;
    // 如果status 不为0 说明已经被选中了，不用再判断了
    if (cur.status !== 0) continue;
    // 拿到坐标
    const { x: x1, y: y1 } = cur;
    // 为了拿到他们的对角坐标，所以要加上100
    //之所以要加上100 是由于 他的总体是800% 也就是一个格子的换算宽度是100
    const x2 = x1 + 100,
      y2 = y1 + 100;
    // 第二个来循环来判断他的覆盖情况
    for (let j = i + 1; j < updateScene.length; j++) {
      const compare = updateScene[j];
      if (compare.status !== 0) continue;


      const { x, y } = compare;
      // 处理交集也就是选中情况
      // 两区域有交集视为选中
      // 两区域不重叠情况取反即为交集
      if (!(y + 100 <= y1 || y >= y2 || x + 100 <= x1 || x >= x2)) {
        // 由于后方出现的元素会覆盖前方的元素，所以只要后方的元素被选中了，前方的元素就不用再判断了
        // 又由于双层循环第二层从j 开始，所以不用担心会重复判断
        cur.isCover = true;
        break;
      }
    }
  }
  scene.value = updateScene;
};

// 弹出
const pop = () => {
  if (!queue.value.length) return;
  const updateQueue = queue.value.slice();
  const symbol = updateQueue.shift();
  if (!symbol) return;
  const find = scene.value.find((s) => s.id === symbol.id);
  if (find) {
    queue.value = updateQueue;
    find.status = 0;
    find.x = 100 * Math.floor(8 * Math.random());
    find.y = 700;
    checkCover(scene.value);
  }
};
// 撤销
const undo = () => {
  if (!queue.value.length) return;
  const updateQueue = queue.value.slice();
  const symbol = updateQueue.pop();
  if (!symbol) return;
  const find = scene.value.find((s) => s.id === symbol.id);
  if (find) {
    queue.value = updateQueue;
    find.status = 0;
    checkCover(scene);
  }
};
// 洗牌
const wash = () => {
  checkCover(washScene(level.value, scene.value));
};

// 下一关
const levelUp = () => {
  if (level.value >= maxLevel) {
    return;
  }
  finished.value = false;
  level.value = level.value + 1;
  queue.value = [];
  checkCover(makeScene(level.value + 1));
};
// 重开
const restart = () => {
  // 初始化内容
  finished.value = false;
  level.value = 1;
  queue.value = [];
  checkCover(makeScene(1));
};

// 点击item
const clickSymbol = async (idx: number) => {
  // 如果已经完成了，就不处理
  if (finished.value || animating.value) return;
  // 拷贝一份Scene
  const symbol = scene.value[idx];
  // 覆盖了和已经在队列里的也不处理
  if (symbol.isCover || symbol.status !== 0) return;
  //置为可以选中状态
  symbol.status = 1;
  queue.value.push(symbol);
  // 制造动画效果中防止点击
  animating.value = true;
  //三百毫秒的延迟
  await waitTimeout(300);

  const filterSame = queue.value.filter((sb) => sb.icon === symbol.icon);

  // 选中的三个配对成功表示已经是三连了
  if (filterSame.length === 3) {
    // 由于icon的类型一样，留下队列中的不一样的剩余内容重新赋值
    queue.value = queue.value.filter((sb) => sb.icon !== symbol.icon);
    // 隐藏iocn，dom
    for (const sb of filterSame) {
      const find = scene.value.find((i) => i.id === sb.id);
      // 将他们的状态变为2 通过opacity 属性 来隐藏icon
      if (find) find.status = 2;
    }
  }

  // 当格子沾满了，那么久表示已经失败了
  if (queue.value.length === 7) {
    tipText.value = '好可惜     再试一次!'
    finished.value = true;
  }

  if (!scene.value.find((s) => s.status !== 2)) {
    // 如果完成所有关卡，那就过了所有关了
    if (level.value === maxLevel) {
      tipText.value = '恭喜你完成挑战！';
      finished.value = true
      return;
    }
    //否则加一关
    level.value = level.value + 1;
    queue.value = []
    // 重新初始化
    checkCover(makeScene(level.value + 1));
  } else {
    // 处理覆盖情况
    checkCover(scene.value);
  }
  // 动画结束
  animating.value = false;
};
// 队列区排序
watchEffect(() => {
  const cache = {};
  // 通过当前的icon的标识，将相同的icon归纳到一块
  // 方便后续排序
  for (const symbol of queue.value) {
    if (cache[symbol.icon]) {
      cache[symbol.icon].push(symbol);
    } else {
      cache[symbol.icon] = [symbol];
    }
  }
  const temp = [];
  for (const symbols of Object.values(cache)) {
    temp.push(...(symbols as any));
  }
  const updateSortedQueue = {};
  let x = 50;
  // 拿到更新后的队列区数据，计算权重
  for (const symbol of temp) {
    updateSortedQueue[symbol.id] = x;
    x += 100;
  }
  //赋值 ，这个是为了将选中的排序后的内容移动到队列区
  sortedQueue.value = updateSortedQueue
  // 检查覆盖情况
  checkCover(scene.value);
})
</script>
<style >
a {
  font-weight: 500;
  color: #646cff;
  text-decoration: inherit;
}

a:hover {
  color: #535bf2;
}


button {
  border-radius: 8px; /*按钮圆角度*/
  border: 1px solid transparent;
  padding: 0.6em 0.5em; /* 距离边缘比例*/
  font-size: 1em;
  font-weight: 800;
  font-family: inherit;
  background-color: #6ca5fe;/*按钮背景颜色*/

  cursor: pointer;
  transition: border-color 0.25s;
  word-break: keep-all;
}

button:hover {
  border-color: #646cff;
}

button:focus,
button:focus-visible {
  outline: 4px auto -webkit-focus-ring-color;
}


#app {
  text-align: center;
  width: 100vw;
  height: 100vh;
  max-width: 500px;
  position: relative;

}

.bg {
  position: absolute;
  background-image: url(./assets/14280553b.png);
  background-size: 100% 100%;
  background-repeat: no-repeat;

  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
}

.box {
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  padding: 50px;/*保证小方块不会出界，原5px*/

}

.app {
  width: 100%;
  margin: 0 auto;
}

.scene-container {
  width: 100%;
  padding-bottom: 100%;
  position: relative;
  margin: 10% 0;/*相对于上下或左右留空的比例*/
}

.scene-inner {
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  overflow: visible;
  font-size: 28px;
}

.symbol {
  width: 12.5%;
  padding-bottom: 12.5%;
  position: absolute;
  transition: 0.3s;
  left: 0;
  top: 0;
}

.symbol-inner {
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 8px;
  border: 2.5px solid #444;/*方块描边粗细*/
  transition: 0.3s;
  padding: 0;/*方块里图片距离边缘距离*/
}

.queue-container {
  border-radius: 8px;/*队列圆角度*/
  width: 100%;
  padding-bottom: 15%;
  border: 2px solid gray;
  margin-bottom: 16px;
  box-sizing: border-box;
  /* background: #1a1a1a url(./assets/14280553b.png); */
}

.flex-container {
  display: flex;
  gap: 8px;/*底下道具按钮彼此之间距离*/
}

.flex-center {
  justify-content: center;
  align-items: center;
}

.flex-grow {
  flex-grow: 1;
}

.flex-between {
  justify-content: space-between;
  align-items: center;
}

.modal {
  position: fixed;
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  backdrop-filter: blur(10px);
  background-color: rgb(0 0 0 / 10%);
  top: 0;
  left: 0;
}
</style>
