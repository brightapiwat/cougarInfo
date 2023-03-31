<template>
    <a-row :gutter="[8,16]"  >
      <a-col :span="24" style="margin-bottom: 10px;">
        <a-row :gutter="[8,16]"  >
          <a-col :span="12" :offset="6" >
            <a-space align="center">
              <a-progress  :percent="(defaultTime*100/1000)" :steps="10" />
            </a-space>
          </a-col>
          <a-col :span="24">
            <a-button-group>
              <a-button @click="decline">
                <template #icon><minus-outlined /></template>
              </a-button>
              <a-button @click="increase">
                <template #icon><plus-outlined /></template>
              </a-button>
            </a-button-group>
          </a-col>
      </a-row>
      </a-col>
      <a-col :span="24" style="margin-bottom: 10px;">
        <a-button type="primary" @click="setCardVisible(!modal1Visible)">
          Test
        </a-button>
      </a-col>
    </a-row>
    <a-card v-show="modal1Visible" style="text-align: center;background-color: rgb(0 49 96);">
      <div v-show="!visibleCard">
        <a-button type="primary" @click="setModal1Visible(!visibleCard)">
          start
        </a-button>
        <a-typography-text strong></a-typography-text >
      </div>
      <div v-show="visibleCard">
        <a-steps v-model:current="indexDataBinding" :disabled="true" >
          <a-step title="" description="" :disabled="true" />
          <a-step title="" description="" :disabled="true"  />
          <a-step title="" description="" :disabled="true" />
          <a-step title="" description="" :disabled="true" />
        </a-steps>
          <input @input="setInput($event, 0)" :disabled="false" ref="dataInp0" class="otp-input" type="text" :maxlength="1" :placeholder="setDataBinding.length != 0 ?setDataBinding[indexDataBinding][0] : ''" />
          <input @input="setInput($event, 1)" :disabled="true" ref="dataInp1" class="otp-input" type="text" :maxlength="1" :placeholder="setDataBinding.length != 0 ?setDataBinding[indexDataBinding][1] : ''" />
          <input @input="setInput($event, 2)" :disabled="true" ref="dataInp2" class="otp-input" type="text" :maxlength="1" :placeholder="setDataBinding.length != 0 ?setDataBinding[indexDataBinding][2] : ''" />
          <input @input="setInput($event, 3)" :disabled="true" ref="dataInp3" class="otp-input" type="text" :maxlength="1" :placeholder="setDataBinding.length != 0 ?setDataBinding[indexDataBinding][3] : ''" />
          <a-progress :percent="(countTimer*100/defaultTime)" status="active" style="min-width: 50px;" :showInfo="false" />
      </div>
      <div style="color: white;">
        {{ secTimer }}
      </div>
      </a-card>
</template>
<script >
import { defineComponent, ref, reactive } from 'vue';
import { MinusOutlined, PlusOutlined } from '@ant-design/icons-vue';
export default defineComponent({
  components: {
    MinusOutlined,
    PlusOutlined,
  },
  
  data() {
    const defaultDataBinding = ['a', 's', 'd', 'w'];
    const modal1Visible = ref(false);
    const setDataBinding = reactive([])
    const dataValue = reactive([])
    const countTimer = ref(0)
    const timerStop = null
    const defaultTime = 500
    const clearDataInp = () => {
      this.$refs['dataInp0'].value = ''
      this.$refs['dataInp1'].value = ''
      this.$refs['dataInp2'].value = ''
      this.$refs['dataInp3'].value = ''
    }

    const clearClassCorrectDataInp = () => {
      this.$refs['dataInp0'].classList.remove('otp-input-correct')
      this.$refs['dataInp1'].classList.remove('otp-input-correct')
      this.$refs['dataInp2'].classList.remove('otp-input-correct')
      this.$refs['dataInp3'].classList.remove('otp-input-correct')
    }

    const addClassWrongDataInp = () => {
      this.$refs['dataInp0'].classList.add('otp-input-wrong')
      this.$refs['dataInp1'].classList.add('otp-input-wrong')
      this.$refs['dataInp2'].classList.add('otp-input-wrong')
      this.$refs['dataInp3'].classList.add('otp-input-wrong')
    }

    const setTimer = () =>{ 
      this.timerStop = setInterval(() => {
        this.countTimer++
        if (this.countTimer >= this.defaultTime) {
          this.clearClassCorrectDataInp()
          this.addClassWrongDataInp()
          const clearData = setInterval(() => {
            this.clearClassCorrectDataInp()
            this.clearClassWrongDataInp()
            this.$refs['dataInp0'].disabled = true
            this.$refs['dataInp1'].disabled = true
            this.$refs['dataInp2'].disabled = true
            this.$refs['dataInp3'].disabled = true
            this.visibleCard = false;
            this.dataSetDefault()
            clearInterval(clearData);
          }, 1000)
         
          clearInterval(this.timerStop); // ยกเลิก setInterval เมื่อนับถอยหลังเสร็จสิ้น
        }
      }, 1); // ระยะเวลาการ setInterval เป็น 1 วินาที
    }
    const clearClassWrongDataInp = () => {
      this.$refs['dataInp0'].classList.remove('otp-input-wrong')
      this.$refs['dataInp1'].classList.remove('otp-input-wrong')
      this.$refs['dataInp2'].classList.remove('otp-input-wrong')
      this.$refs['dataInp3'].classList.remove('otp-input-wrong')
    }
    
    const dataSetDefault = () => {
      this.setDataBinding = []
      this.indexDataBinding = 0
      for (let index = 0 ; index < 4; index++) {
        let data1 = this.defaultDataBinding[Math.floor(Math.random()* this.defaultDataBinding.length)]
        let data2 = this.defaultDataBinding[Math.floor(Math.random()* this.defaultDataBinding.length)]
        let data3 = this.defaultDataBinding[Math.floor(Math.random()* this.defaultDataBinding.length)]
        let data4 = this.defaultDataBinding[Math.floor(Math.random()* this.defaultDataBinding.length)]
        let dataSet = [
          data1,
          data2,
          data3,
          data4,
        ]
        this.dataValue.push([])
        this.setDataBinding.push(dataSet)
      }
      this.timer = Date.now() + 2500
      this.$nextTick(() => {
        this.$refs['dataInp0'].disabled = false
        this.$refs['dataInp0'].focus();
        this.clearDataInp()
        this.countTimer = 0
      });
    }

    const increase = () => {
      const percent = this.defaultTime + 100;
      this.defaultTime = percent >= 1000 ? 1000 : percent;
    };

    const decline = () => {
      const percent = this.defaultTime - 100;
      this.defaultTime = percent <= 300 ? 300 : percent;
    };

    return {
      timerStop,
      addClassWrongDataInp,
      setTimer,
      defaultTime,
      countTimer,
      modal1Visible,
      defaultDataBinding,
      setDataBinding,
      clearDataInp,
      indexDataBinding : ref(0),
      timer: Date.now() + 1000,
      visibleCard: ref(false),
      secTimer: ref(),
      dataSetDefault,
      dataValue,
      clearClassCorrectDataInp,
      clearClassWrongDataInp,
      increase,
      decline,
    };
  },
  methods: {
    setCardVisible: async function(visible) {
      this.modal1Visible = visible;
      this.visibleCard = false
    },
    setModal1Visible: async function(visible) {
      let count = 3; // ตั้งค่าเริ่มต้นสำหรับการนับถอยหลัง
      this.countTimer = 0
      this.secTimer = count;
      const countdownTimer = setInterval(() => {
        count--; // ลดค่าทีละ 1 วินาที
        this.secTimer = count;
        if (count === 0) {
          clearInterval(countdownTimer); // ยกเลิก setInterval เมื่อนับถอยหลังเสร็จสิ้น
          this.visibleCard = visible;
          this.setTimer()
          this.dataSetDefault()
        }
      }, 1000); // ระยะเวลาการ setInterval เป็น 1 วินาที
    },
    setInput: function(e,index) {
      if (e.target.value != this.setDataBinding[this.indexDataBinding][index]) {
        this.$refs['dataInp'+(index)].classList.add('otp-input-wrong')
        this.$refs['dataInp'+(index)].disabled = true
        clearInterval(this.timerStop);
        const countdownTimerStop = setInterval(() =>  {
          this.clearClassCorrectDataInp()
          this.clearClassWrongDataInp()
          this.visibleCard = false;
          this.dataSetDefault()
          clearInterval(countdownTimerStop); // ยกเลิก setInterval เมื่อนับถอยหลังเสร็จสิ้น
        }, 1500); // ระยะเวลาการ setInterval เป็น 1 วินาที
        //// do something
      } else {
        this.$refs['dataInp'+(index)].classList.add('otp-input-correct')
        if (index == 3) {
          clearInterval(this.timerStop)
          if (this.indexDataBinding != 3) {
            this.clearDataInp()
            this.$refs['dataInp'+(index)].disabled = true
            const countdownTimer = setInterval(() => {
              this.clearClassCorrectDataInp()
              this.clearClassWrongDataInp()
              this.$refs['dataInp0'].disabled = false
              this.$refs['dataInp0'].focus(); 
              this.indexDataBinding = this.indexDataBinding + 1
              this.countTimer = 0
              this.setTimer()
              clearInterval(countdownTimer); // ยกเลิก setInterval เมื่อนับถอยหลังเสร็จสิ้น
            }, 500); // ระยะเวลาการ setInterval เป็น 1 วินาที
          } else {
            this.$refs['dataInp'+(index)].disabled = true
            const countdownTimerStop = setInterval(() => {
              this.clearClassCorrectDataInp()
              this.clearClassWrongDataInp()
              this.visibleCard = false;
              this.dataSetDefault()
              clearInterval(countdownTimerStop); // ยกเลิก setInterval เมื่อนับถอยหลังเสร็จสิ้น
            }, 1500); // ระยะเวลาการ setInterval เป็น 1 วินาที
            
          }
        } else {
          this.$refs['dataInp'+(index+1)].disabled = false
          this.$refs['dataInp'+(index)].disabled = true
          this.$refs['dataInp'+(index+1)].focus();

        }
      }
    }
  },
  mounted() {
    clearInterval(this.timerStop)
  },
  computed: {
  }
});
</script>
<style scoped>
.otp-input {
  height: 100px;
  width: 100px;
  margin: 5px;
  font-size: xx-large;
  text-align: center;
  border: none;
  caret-color: transparent;
}

.otp-input:disabled {
  background-color: gray;
  color: white;
}

.otp-input::placeholder {
  font-size: xx-large;
}

.otp-input:disabled::placeholder {
  color: white;
}
.otp-input-correct {
  background-color: #7fff74 !important;
  color: #000000  !important;
}
.otp-input-wrong {
  background-color: #ff6c6c !important;
  color: #000000  !important;
}
</style>