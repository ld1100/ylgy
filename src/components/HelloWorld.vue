<template>
  <div class="hello">
    <div class="top">
      <div class="card-box">
        <div v-for="(item,index) in cardList" :key="index" :style="{top:item.top,left:item.left}" class="card"
             @click="cardClick(item,index,$event)">
          <!--          <i :class="item.icon"></i>-->
          <img :src="item.icon">
        </div>
      </div>
    </div>
    <el-row justify="center" type="flex">
      <div class="bottom">
        <div v-for="(item,index) in selectedCardList" :key="index" class="bottom-card">
          <!--          <i :class="item.icon"></i>-->
          <img :src="item.icon">
        </div>
      </div>
    </el-row>
    <el-row justify="center" type="flex">
      <el-button plain type="primary" @click="initData">重新开始</el-button>
      <el-button plain type="primary" @click="dialogVisible=true">切换难度（
        <span v-if="currentDifficulty==='primary'">初级</span>
        <span v-if="currentDifficulty==='middle'">中级</span>
        <span v-if="currentDifficulty==='advanced'">高级</span>
        ）
      </el-button>
      <el-button :disabled="backAccount===0||selectedHistory.length===0" plain type="primary"
                 @click="cardBack">撤回（剩余{{ backAccount }}次）
      </el-button>
    </el-row>
    <el-dialog
        :visible.sync="dialogVisible"
        title="选择难度"
        width="300px">
      <div class="dialog-content">
        <el-radio-group v-model="currentDifficulty" @input="switchDifficulty">
          <el-radio :label="'primary'" border size="medium">初级难度</el-radio>
          <el-radio :label="'middle'" border size="medium">中级难度</el-radio>
          <el-radio :label="'advanced'" border size="medium">高级难度</el-radio>
        </el-radio-group>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      cardList: [],
      positionList: [],
      selectedCard: {},
      selectedCardList: [],
      selectedHistory: [],
      // iconList: [
      //   { picIndex: 0, icon: 'el-icon-food' },
      //   { picIndex: 1, icon: 'el-icon-chicken' },
      //   { picIndex: 2, icon: 'el-icon-tableware' },
      //   { picIndex: 3, icon: 'el-icon-burger' },
      //   { picIndex: 4, icon: 'el-icon-ice-cream' },
      //   { picIndex: 5, icon: 'el-icon-cold-drink' },
      //   { picIndex: 6, icon: 'el-icon-grape' },
      //   { picIndex: 7, icon: 'el-icon-watermelon' },
      //   { picIndex: 8, icon: 'el-icon-cherry' },
      //   { picIndex: 9, icon: 'el-icon-apple' },
      //   { picIndex: 10, icon: 'el-icon-orange' },
      //   { picIndex: 11, icon: 'el-icon-pear' },
      //   { picIndex: 12, icon: 'el-icon-potato-strips' },
      //   { picIndex: 13, icon: 'el-icon-lollipop' },
      //   { picIndex: 14, icon: 'el-icon-ice-cream-square' }
      // ],  // 图标类型数组
      iconList: [
        { picIndex: 0, icon: require('@/assets/images/企鹅.png') },
        { picIndex: 1, icon: require('@/assets/images/刺猬.png') },
        { picIndex: 2, icon: require('@/assets/images/猫头鹰.png') },
        { picIndex: 3, icon: require('@/assets/images/小狗.png') },
        { picIndex: 4, icon: require('@/assets/images/小狐狸.png') },
        { picIndex: 5, icon: require('@/assets/images/小羊.png') },
        { picIndex: 6, icon: require('@/assets/images/青蛙.png') },
        { picIndex: 7, icon: require('@/assets/images/小鸭子.png') },
        { picIndex: 8, icon: require('@/assets/images/河豚.png') },
        { picIndex: 9, icon: require('@/assets/images/水獭.png') },
        { picIndex: 10, icon: require('@/assets/images/狮子.png') },
        { picIndex: 11, icon: require('@/assets/images/小老虎.png') },
        { picIndex: 12, icon: require('@/assets/images/小猫咪.png') },
        { picIndex: 13, icon: require('@/assets/images/小兔子.png') },
        { picIndex: 14, icon: require('@/assets/images/羊驼.png') }
      ],  // 图标类型数组
      dialogVisible: false,
      currentDifficulty: 'primary',
      cardTotal: 102, //游戏card总数
      backAccount: 3  //剩余撤回次数
    }
  },
  created() {
    this.initData()
  },
  methods: {
    cardBack() {
      if (this.selectedHistory.length > 0 && this.backAccount > 0) {
        const backItem = this.selectedHistory[this.selectedHistory.length - 1]
        this.cardList.push(backItem)
        this.selectedHistory = this.selectedHistory.filter(item => {
          return item.id != backItem.id
        })
        this.selectedCardList = this.selectedCardList.filter(item => {
          return item.id != backItem.id
        })
        this.backAccount--
      }
    },
    switchDifficulty() {
      if (this.currentDifficulty === 'primary') {
        this.cardTotal = 102
      } else if (this.currentDifficulty === 'middle') {
        this.cardTotal = 303
      } else {
        this.cardTotal = 501
      }
      this.initData()
      this.dialogVisible = false
    },
    cardClick(item, index, event) {
      const isHover = this.hasOverLayer(event.srcElement)
      this.selectedCard = item
      // console.log(isHover)
      if (!isHover) {
        this.cardList.splice(index, 1)
        this.selectedHistory.push(item)
        this.selectedCardList.push(item)
        // 排序，将相同的图标排到一起
        this.selectedCardList.sort((a, b) => {
          return a.picIndex - b.picIndex
        })

        // 三个相同的进行消除
        this.removeCard()
        setTimeout(() => {
          if (this.cardList.length === 0) {
            alert('恭喜通关！')
            this.initData()
          }
          if (this.selectedCardList.length >= 7) {
            alert('游戏失败！')
            this.initData()
          }
        }, 200)
        console.log(this.cardList.length)
      }
    },
    removeCard() {
      let num = 0
      this.selectedCardList.forEach(item => {
        if (item.icon === this.selectedCard.icon) {
          num++
        }
      })
      if (num === 3) {
        this.selectedCardList = this.selectedCardList.filter(item => {
          return item.icon !== this.selectedCard.icon
        })
        this.selectedHistory = this.selectedHistory.filter(item => {
          return item.icon !== this.selectedCard.icon
        })
      }
    },
    // 监测元素是否被覆盖
    hasOverLayer(element) {
      let document = element.ownerDocument,
          rect = element.getBoundingClientRect(), // 获取目标的矩形信息
          x = rect.x,
          y = rect.y,
          width = rect.width,
          height = rect.height
      x |= 0
      y |= 0
      width |= 0
      height |= 0
      // 四顶点取样
      let elements = [
        document.elementFromPoint(x + 1, y + 1),
        document.elementFromPoint(x + width - 1, y + 1),
        document.elementFromPoint(x + 1, y + height - 1),
        document.elementFromPoint(x + width - 1, y + height - 1)
      ]
      // 判断非本身及非子孙元素
      return elements.filter((el) => el !== null).some((el) => el !== element && !element.contains(el))
    },

    initData() {
      // 位置初始化124个位置
      let index = 0
      // 第一层位置
      let left = 0
      let top = 0
      for (let i = 0; i < 10; i++) {
        for (let j = 0; j < 7; j++) {
          this.positionList.push({
            id: index++,
            picIndex: 1,
            icon: 'el-icon-star-on',
            top: top + 'px',
            left: left + 'px'
          })
          left = left + 50
        }
        top = top + 50
        left = 0
      }
      // 第二层位置
      left = 25
      top = 25
      for (let i = 0; i < 9; i++) {
        for (let j = 0; j < 6; j++) {
          this.positionList.push({
            id: index++,
            picIndex: 1,
            icon: 'el-icon-star-on',
            top: top + 'px',
            left: left + 'px'
          })
          left = left + 50
        }
        top = top + 50
        left = 25
      }
      this.createIcon()
    },
    // 生成图标
    createIcon() {
      let index = 0
      this.cardList = []
      this.selectedCard = {}
      this.selectedCardList = []
      this.selectedHistory = []
      this.backAccount = 3
      let random = 0
      // 生成300个图标
      for (let i = 0; i < this.cardTotal; i++) {
        if (i % 3 == 0) {
          random = Math.floor(Math.random() * 15)
        }
        this.cardList.push({
          id: index++,
          icon: this.iconList[random].icon,
          picIndex: this.iconList[random].picIndex
        })
      }
      //对300个图标随机分配位置
      let position = 0
      this.cardList.forEach(item => {
        position = Math.floor(Math.random() * 124)
        item['left'] = this.positionList[position].left
        item['top'] = this.positionList[position].top
      })
    }
  }

}
</script>

<style lang="scss" scoped>
.top {
  height: 500px;
  background-color: #efefec;
}

.card-box {
  width: 350px;
  height: 100%;
  margin: 0 auto;
  position: relative;

  .card {
    width: 50px;
    height: 50px;
    text-align: center;
    line-height: 50px;
    border: 1px solid #cccccc;
    box-shadow: 0 0 5px 0 #b6b5b5;
    position: absolute;
    background-color: #FFFFFF;
    font-size: 36px;
    cursor: pointer;
    padding-top: 5px;

    img {
      max-height: 85%;
    }
  }
}

/*.el-icon-food {
  color: #022459;
}

.el-icon-chicken {
  color: #8cd005;
}

.el-icon-tableware {
  color: #6b6be7;
}

.el-icon-burger {
  color: #064ef5;
}

.el-icon-ice-cream {
  color: #fc3967;
}

.el-icon-cold-drink {
  color: #f5d503;
}

.el-icon-grape {
  color: #9022f5;
}

.el-icon-watermelon {
  color: #03703a;
}

.el-icon-apple {
  color: #ff3838;
}

.el-icon-cherry {
  color: #9a0707;
}

.el-icon-orange {
  color: #f86c15;
}

.el-icon-potato-strips {
  color: #867508;
}

.el-icon-pear {
  color: #347257;
}

.el-icon-lollipop {
  color: #c41091;
}

.el-icon-ice-cream-square {
  color: #0bbde5;
}*/


.bottom {
  margin: 25px 0;
  height: 55px;
  width: 350px;
  background-color: #efefec;
  display: flex;
  align-items: center;

  .bottom-card {
    width: 50px;
    height: 50px;
    text-align: center;
    line-height: 50px;
    border: 1px solid #FFFFFF;
    box-shadow: 0 0 5px 0 #777676;
    background-color: #FFFFFF;
    font-size: 36px;
    cursor: pointer;
    padding-top: 5px;

    img {
      max-height: 85%;
    }
  }
}

.dialog-content {
  width: 150px;
  margin: 0 auto;

  .el-radio-group {
    text-align: center;
  }

  .el-radio {
    margin: 5px auto;
  }

  .el-radio.is-bordered + .el-radio.is-bordered {
    margin-left: 0;
  }
}
</style>
