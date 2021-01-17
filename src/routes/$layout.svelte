<script context="module">
	export async function load({ page, context }) {
        console.log("Load function called in $layout with page", page);
        return { context: { info: "Info from layout load() function" } };
	}
</script>

<p>Header goes here</p>

<slot></slot>

<p>Footer goes here</p>