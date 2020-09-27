<template>
	<div id="app">
		<p class="title">Hex Editor</p>
		<Uploader v-on:giveBinaryData="gotBinaryData"/>
		<HexEditor v-if="binaryData" :binaryData="binaryData" :perPage="4096"/>
	</div>
</template>

<script>
import HexEditor from './components/HexEditor.vue';
import Uploader from './components/Uploader.vue';

export default {
	name: 'App',
	components: {
		HexEditor,
		Uploader,
	},
	data: function() {
		return {
			binaryData: '',
		}
	},
	methods: {
		gotBinaryData: function(binary) {
			this.binaryData = binary;
		},
	},
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
