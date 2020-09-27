<template>
	<b-field>
		<b-button @click="gotFile">Convert To HEX</b-button>
		<b-upload v-model="file" class="file-label">
			<span class="file-cta">
				<b-icon class="file-icon" icon="upload"></b-icon>
				<span class="file-label">Click to upload</span>
			</span>
			<span class="file-name" v-if="file">
				{{ file.name  }}
			</span>
		</b-upload>
	</b-field>
</template>

<script>
export default {
	name: 'Uploader',
	data: function() {
		return {
			file: null
		}
	},
	methods: {
		gotFile: function() {
			let reader = new FileReader();
			reader.addEventListener('loadend', (e) => {
				let tmp = [];
				let len = 1024;
				for(let p = 0; p < e.target.result.byteLength; p += len) {
					tmp.push(this.bufferToString(e.target.result.slice(p, p + len)));
				}
				console.log('converted');
				this.$emit('giveBinaryData', tmp.join(""));
			});
			//reader.readAsBinaryString(event.target.files[0]);
			reader.readAsArrayBuffer(this.file);
		},
		bufferToString: function(buf) {
			return String.fromCharCode.apply("", new Uint8Array(buf));
		}
	}
}
</script>
