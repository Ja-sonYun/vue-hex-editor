<template>
	<div id="fileupload">
		<b-field>
			<b-upload v-model="file"
				drag-drop>
				<section class="section">
					<div v-if="file" class="tags">
						<span class="tag is-primary">
							{{file.name}}
						</span>
					</div>
					<div v-else class="content has-text-centered">
						<p>Drop your file or click to upload.</p>
					</div>
				</section>
			</b-upload>
		</b-field>
		<div v-if="file">
			<b-button size="is-small" @click="gotFile">
				Convert to Hex
			</b-button>
			<b-button size="is-small" @click="deleteDropFile">
				Cancel
			</b-button>
		</div>
	</div>
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
		},
		deleteDropFile: function() {
			this.file = null;
		}
	}
}
</script>

<style>
#fileupload {
	padding-top: 50px;
	text-align: center;
}
</style>
