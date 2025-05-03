<script lang="ts">
	import { page } from '$app/state';
</script>

<h1 class="text-2xl">{page.error ? page.error.message : '알 수 없는 에러입니다.'}</h1>