<svelte:head>
  <title>인포스낵</title>
</svelte:head>

<script lang="ts">
  import { onMount, afterUpdate } from 'svelte';

  // Instagram 퍼머링크를 담을 변수
  let emurl = "";

  // Instagram embed.js 스크립트를 한 번만 로드
  function loadInstagramScript() {
    if (!document.querySelector('script[src="https://www.instagram.com/embed.js"]')) {
      const script = document.createElement('script');
      script.src = 'https://www.instagram.com/embed.js';
      script.async = true;
      document.head.appendChild(script);
    }
  }

  // emurl을 fetch해서 세팅하고, 세팅 직후에 모든 .rfse 블록을 갱신
  onMount(async () => {
    loadInstagramScript();

    try {
      const rawUrl = 'https://raw.githubusercontent.com/jxnuhy/many_texts/main/gghs';
      const res = await fetch(rawUrl);
      if (!res.ok) throw new Error(`Fetch failed: ${res.status}`);
      const text = await res.text();
      const prt = text.slice(0, 11);
      emurl = `https://www.instagram.com/p/${prt}/?utm_source=ig_embed&utm_campaign=loading`;
      // emurl이 세팅된 직후에 퍼머링크 갱신
      updateRfsePermalinks(emurl);
    } catch (err) {
      console.error('Fetch error:', err);
    }
  });

  // emurl이 바뀔 때마다 afterUpdate 훅에서 임베드 처리
  afterUpdate(() => {
    if (emurl && window.instgrm?.Embeds?.process) {
      // DOM이 완전히 변경된 뒤 실행
      setTimeout(() => {
        window.instgrm.Embeds.process();
      }, 50);
    }
  });

  // .rfse 클래스를 가진 blockquote의 data-instgrm-permalink을 모두 업데이트
  // @ts-ignore
  function updateRfsePermalinks(newUrl: string) {
    const elems = document.querySelectorAll<HTMLElement>('.rfse');
    elems.forEach(el => {
      // dataset 또는 setAttribute 두 가지 방식 중 선택
      el.dataset.instgrmPermalink = newUrl;
      // el.setAttribute('data-instgrm-permalink', newUrl);
    });
  }
</script>

<style>
  :global(body) {
    font-family: NanumSquareNeoBold;
  }
  .nanum-heavy { font-family: NanumSquareNeoHeavy; }
  .nanmu-eb { font-family: NanumSquareNeoExtraBold; }
</style>

<div class="flex flex-col items-center justify-center w-full h-full p-2 box-border antialiased md:subpixel-antialiased bg-neutral-50">
  <div class="text-center flex flex-col justify-center items-center mt-2 h-48 bg-yellow-500 text-white rounded-2xl w-full">
    <div class="nanmu-eb text-lg">
      2025학년도 기흥고등학교<br>
      학생주도성 프로젝트 봉사동아리
    </div>
    <div class="nanum-heavy text-4xl mt-3">인포스낵</div>
  </div>

  <div class="flex flex-col justify-center items-center text-center mt-2 h-90 bg-violet-50 text-gray-900 rounded-2xl w-full">
    유용한 바로가기들 (⬇️)<br><br>
    <a class="text-3xl mt-4 mr-9 bg-pink-200 py-3 nanum-heavy px-2 rounded-2xl rotate-354" href="https://www.instagram.com/ghhs_info_snack_/">
      🔗 인스타그램으로 가기&nbsp;
    </a>
    <a class="text-3xl mt-10 ml-9 bg-blue-200 py-3 nanum-heavy px-2 rounded-2xl rotate-6" href={emurl}>
      🔗 추천 게시물로 가기&nbsp;
    </a>
  </div>

  <div class="flex flex-col justify-center items-center text-center mt-2 h-180 bg-violet-50 text-gray-900 rounded-2xl w-full">
    
    <div class="text-3xl text-gray-900 pb-5 nanum-heavy">추천 컨텐츠</div>

    <div>
      {#if emurl}
      <!-- emurl이 비어있지 않을 때만 렌더링 -->
      <blockquote
        class="instagram-media rfse"
        data-instgrm-permalink={emurl}
        data-instgrm-version="14"
        style="
          background:#FFF; border:0; border-radius:3px;
          box-shadow:0 0 1px 0 rgba(0,0,0,0.5),
                    0 1px 10px 0 rgba(0,0,0,0.15);
          margin:1px; max-width:540px; min-width:326px;
          padding:0; width:calc(100% - 2px);
        ">
        <!-- 로딩 플레이스홀더는 Instagram 스크립트가 삽입 -->
      </blockquote>
      {/if}
      {#if !emurl}
        <span class="loading loading-dots loading-lg"></span>
        <div class="text-xl nanum-eb">Now Loading...</div>
      {/if}
    </div>
  </div>

  <div class="text-center flex flex-col justify-center items-center mt-2 h-70 bg-gray-700 text-neutral-50 rounded-2xl w-full">
    ⓒ Copyright 2025. 2025학년도<br>
    기흥고등학교 학생주도성 프로젝트<br>
    봉사동아리 인포스낵. All Rights Reserved.
    <br><br>· 이 사이트는 기흥고등학교의 공식 사이트가 아니며, <br>학교의 의견을 대변하지 않습니다.
    <br><br><a href="">Source Code(GitHub)</a>
  </div>
</div>