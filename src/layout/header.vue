<template>
	<section>
		<p id="abex"><a class="AbutBlack" href="https://abex.dev">abex.dev</a></p>
		<div id="header">
			<p id="title">Online Hex Editor</p>
			<p id="subtitle">made with vue.js.</p>
		</div>
	</section>
</template>

<script>

export default {
	name: 'Header',
}

</script>

<style>

#header {
	text-align: center;
}
#abex {
	font-weight: bold;
	position: absolute;
	margin-top: -45px;
	padding-left: 15px;
	font-family: "Lucida Console", Courier, monospace;
}

.AbutBlack {
	color: black;
}

#title {
	padding: 40px;
	font-size: 44px;
}

</style>
