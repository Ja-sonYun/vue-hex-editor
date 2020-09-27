<template>
	<div id="HexEditor">
		<div class="paginate">
			<b-pagination
				:total="binaryData.length"
				size="is-small"
				v-model="current"
				order="is-centered"
				:per-page="perPage">
				<b-pagination-button
					slot-scope="props"
					:page="props.page"
					:id="`page${props.page.number}`">
					{{ (props.page.number-1) }}k~
				</b-pagination-button>
			</b-pagination>
		</div>
		<div id="searchBar">
			<b-field label="from" label-position="on-border">
				<b-select placeholder="from" size="is-small" class="convertSelection" v-model="fromType">
					<option v-for="type in convertableTypes"
							:value="type.i"
							:key="type.type">{{ type.type }}</option>
				</b-select>
			</b-field>
			<b-field label="to" label-position="on-border">
				<b-select placeholder="to" size="is-small" class="convertSelection" v-model="toType">
					<option v-for="type in convertableTypes"
							:value="type.i"
							:key="type.type">{{ type.type }}</option>
				</b-select>
			</b-field>
			<b-field label="convert" label-position="on-border">
				<b-input size="is-small" v-model="convert" placeholder="000000"></b-input>
				<b-button size="is-small" @click="convertNumber" type="is-info">go</b-button>
			</b-field>
			<div class="wrapSeperateInsideSearchBar">
				<div class="sperateInsideSearchBar"></div>
			</div>
			<b-field label="go to" label-position="on-border" id="afterSeperateLine">
				<b-input size="is-small" v-model="search" placeholder="0x000000"></b-input>
				<b-button size="is-small" @click="gotoAddress" type="is-info">move</b-button>
			</b-field>
		</div>
		<table v-if="until" width="800" class="hex">
			<thead>
				<th class="stickOnTop" width="70" style="color:red">>>>_</th>
				<th class="stickOnTop" v-for="(e, i) in 16" :key="i" width="35">{{ '0'+i.toString(16).toUpperCase() }}</th>
				<th class="stickOnTop" width="20"></th>
				<th class="stickOnTop" width="20">ascii</th>
			</thead>
			<tbody>
				<tr v-for="(w, y) of Math.ceil(perPage/16)" :key="y+'_tr'">
					<td :id="y+'y'" style="text-align:left">{{ giveAddress(y*=16) }}</td>
					<td v-for="(e, x) in 16" :key="x+'_rd'">
						<template v-if="binaryDataSliced[w=x+y] != undefined">
							<a :id="w" @click="editHex(w)" >{{ binaryDataSliced[w].hexEncode().toUpperCase() }}</a>
						</template>
					</td>
					<td></td>
					<td style="text-align:left">{{ binaryDataSliced.slice(w-15, w+1).hexEncode().hex2Ascii().replaceNonAscii() }}</td>
				</tr>
			</tbody>
		</table>
	</div>
</template>

<script>

export default {
	name: 'HexEditor',
	props: {
		binaryData: String,
		perPage: Number, // stored as decimal
	},
	data: function() {
		return {
			binaryDataSliced: '',
			addressMaxLength: 0,
			search: '', // store as hex
			beforeConvert: 0,
			convert: 0,
			toType: '',
			fromType: '',
			convertableTypes: [ {'type': 'decimal', 'i':0}, {'type': 'hexadecimal', 'i':1}, {'type': 'binary', 'i':2}, {'type': 'ascii', 'i':3} ],
			showingConvertedValue: false,
			current: 1,
			until: 0,
		}
	},
	methods: {
		editHex(index) {
			let byte = this.binaryData[index].hexEncode().toUpperCase();
			this.$buefy.dialog.prompt({
				message: `This Hex is at <strong>${index.toString(16).toUpperCase()}</strong>. change <strong>0x${byte}</strong> to =>`,
				inputAttrs: {
					placeholder: '00',
					maxlength: 2
				},
				trapFocus: true,
				onConfirm: ((value) => {
					document.getElementById(index).className = "editedHex";
					// this.$buefy.toast.open(`Hex edited from ${byte} to ${value}.`)
					this.$buefy.toast.open(`Hex editor is on progress... You entered ${byte} to ${value}`)
				})
			})
		},
		giveAddress(adr) {
			adr = adr.toString(16).toUpperCase();
			if(adr.length < this.addressMaxLength) {
				return '0x' + '0'.repeat(this.addressMaxLength - adr.length) + adr;
			}
			return '0x' + adr;
		},
		gotoAddress() {
			let moveTo = parseInt(this.search, 16);
			if(this.search && moveTo < this.until) {
				let element = document.getElementById(moveTo);
				element.scrollIntoView({ behavior: 'smooth', block: 'end' })
				this.highlightElement(element);
				this.$buefy.toast.open(`moving to ${this.search}.`);
			} else {
				this.$buefy.toast.open(`please enter the valid address.`)
			}
		},
		convertNumber() {
			this.beforeConvert = this.convert;
			switch(this.fromType) {
				case 0: // decimal
					switch(this.toType) {
						case 0: // decimal to decimal
							this.$buefy.snackbar.open(`Wrong types.`);
							break;
						case 1: // decimal to hex
							this.showConvertedValue(this.convert.toString(16).toUpperCase());
							break;
						case 2: // decimal to binary
							this.showConvertedValue(this.convert.toString(2));
							break;
						case 3: // decimal to binary
							this.showConvertedValue(this.convert.toString(16).hex2Ascii());
							break;
					}
					break;
				case 1: // hex
					switch(this.toType) {
						case 0: // hex to decimal
							this.showConvertedValue(parseInt(this.convert, 16));
							break;
						case 1:
							this.$buefy.snackbar.open(`Wrong types.`);
							break;
						case 2:
							this.showConvertedValue(parseInt(this.convert, 16).toString(2));
							break;
						case 3: // decimal to binary
							this.showConvertedValue(this.convert.toString(16).hex2Ascii());
							break;
					}
					break;
				case 2: // binary
					switch(this.toType) {
						case 0:
							this.showConvertedValue(parseInt(this.convert, 2));
							break;
						case 1:
							this.showConvertedValue(parseInt(this.convert, 2).toString(16).toUpperCase());
							break;
						case 2:
							this.$buefy.snackbar.open(`Wrong types.`);
							break;
						case 3: // decimal to binary
							this.showConvertedValue(parseInt(this.convert, 2).toString(16).hex2Ascii());
							break;
					}
					break;
				case 3: // binary
					switch(this.toType) {
						case 0:
							this.showConvertedValue(parseInt(this.convert.ascii2Hex(), 16));
							break;
						case 1:
							this.showConvertedValue(this.convert.ascii2Hex());
							break;
						case 2:
							this.showConvertedValue(parseInt(this.convert.ascii2Hex(), 16).toString(2));
							break;
						case 3: // decimal to binary
							this.$buefy.snackbar.open(`Wrong types.`);
							break;
					}
					break;
			}
		},
		showConvertedValue(value) {
			this.showingConvertedValue = false;
			this.showingConvertedValue = true;
			this.$buefy.snackbar.open({
				message: `${this.convertableTypes[this.fromType].type}(${this.beforeConvert}) to ${this.convertableTypes[this.toType].type} is ${value}.`,
				type: 'is-success',
				position: 'is-bottom',
				actionText: 'Close',
				indefinite: this.showingConvertedValue,
				onAction: () => {
					this.showingConvertedValue = false
				}
			});
		},
		highlightElement(document) {
			document.className = 'highlight';
			setTimeout(() => {
				document.className = 'removeHighlight';
				setTimeout(() => {
					document.className = '';
				}, (1000));
			}, (3000));
		},
	},
	mounted() {
		this.until = this.perPage > this.binaryData.length ? this.binaryData.length : this.perPage;
		this.binaryDataSliced = this.binaryData.slice(0, this.perPage);
	},
	watch: {
		until: function() { //TODO: check sequence
			this.addressMaxLength = this.until.toString(16).length;
		},
		current: function(val) {
			let frontAddress = (val - 1) * this.perPage;
			this.until = frontAddress + this.perPage;
			this.binaryDataSliced = this.binaryData.slice(frontAddress, frontAddress + this.perPage);
		}
	},
}

String.prototype.replaceNonAscii = function() {
	let str = this.toString();
	str = str.replace(/\x20/g, '_');
	return str.replace(/[^\x20-\x7E]/g, '.');
}

String.prototype.hex2Ascii = function() {
	let hex  = this.toString();
	let str = '';
	for (let n = 0; n < hex.length; n += 2) {
		str += String.fromCharCode(parseInt(hex.substr(n, 2), 16));
	}
	return str;
}

String.prototype.ascii2Hex = function() {
	let arr1 = [];
	for (let n = 0, l = this.length; n < l; n ++) {
		let hex = Number(this.charCodeAt(n)).toString(16);
		arr1.push(hex);
	}
	return arr1.join('');
}

String.prototype.hexEncode = function() {
	let hex, i;
	let result = "";
	for(i=0; i<this.length; i++) {
		hex = this.charCodeAt(i).toString(16);
		if(hex.length < 2) { hex = "0" + hex }
		result += (hex).slice(-4);
	}

	return result;
}

String.prototype.hexDecode = function() {
	let i;
	let hexes = this.match(/.{1,4}/g) || [];
	let back = "";
	for(i=0; i<hexes.length; i++) {
		back += String.fromCharCode(parseInt(hexes[i], 16))
	}

	return back;
}

</script>

<style>
.stickOnTop {
	position: -webkit-sticky;
	position: sticky;
	top: 0;
	background-color: white;
}
.hex {
	font-family: monospace;
	text-align: center;
}
.editedHex {
	color:blue;
	font-weight:bold;
}
.highlight {
	background-color: red;
	border-radius: 5px;
	--webkit-transition: background-color 2s ease-out;
	-moz-transition: background-color 2s ease-out;
	-o-transition: background-color 2s ease-out;
	transition: background-color 2s ease-out;
}
.removeHighlight {
	background-color: ;
	border-radius: 5px;
	--webkit-transition: background-color 1s ease-out;
	-moz-transition: background-color 1s ease-out;
	-o-transition: background-color 1s ease-out;
	transition: background-color 1s ease-out;
}
#searchBar {
	position: fixed;
	padding: 12px;
	border-radius: 5px;
	backdrop-filter: blur(10px);
	bottom: 0;
	right: 0;
	width: 170px;
}
.convertSelection {
	margin-left: -35px;
	width: 130;
}
.wrapSeperateInsideSearchBar {
	padding-bottom: 10px;
}
.wrapSeperateInsideSearchBar .sperateInsideSearchBar {
	margin-left: -4px;
	width: 154px;
	height: 0;
	border: 1px solid gray
}
#HexEditor {
	padding-bottom: 200px;
	padding-left: 10px;
}
.pagination {
	padding: 20px;
}
</style>
