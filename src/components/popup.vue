<template>
  <div id="popup">
    <p>Something is going wrong. Try again</p>
    <button @click="close">OK</button>
  </div>
</template>

<script>
export default {
  name: 'popup',
  methods: {
    close() {
      this.$emit("close");
    }
  }
}
</script>

<style scoped>
#popup{
  position: absolute;
  top: 14vw;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 2vw;
  background-color: #fff;
  border: .3vw solid #25252584;
}
p{
  margin-bottom: 2vw;
  font-size: 2.2vw;
}
button{
  padding: .6vw 2vw;
  background-color: #252525;
  border: 0;
  border-radius: 2vw;
  color: #fff;
  font-size: 1.2vw;
  transition: .2s;
}
button:hover{
  background-color: #464646;
}
button:active{
  background-color: #111111;
}

</style>
