<template>
	<div id="app">
		<Header />
		<hr>
		<div class="main-content">
			<Uploader v-if="!binaryData" v-on:giveBinaryData="gotBinaryData"/>
			<HexEditor v-if="binaryData" :binaryData="binaryData" :perPage="4096"/>
		</div>
		<Footer />
	</div>
</template>

<script>
import HexEditor from './components/HexEditor.vue';
import Uploader from './components/Uploader.vue';
import Footer from './layout/footer.vue';
import Header from './layout/header.vue';

export default {
	name: 'App',
	components: {
		HexEditor,
		Uploader,
		Footer,
		Header,
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
  position: relative;
  min-height: 100vh;
}

.main-content {
	padding-bottom: 50px;
}
</style>
