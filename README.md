# webstories
create amp stories in next js application.

## Install
Create a new empty nextjs app with command `npx create-next-app@latest --ts`.
then go to index.tsx and replace with below code
```
import type { NextPage } from "next";
import Script from "next/script";
import React, { useEffect } from "react";

// mysite.com/stories/cHome#3
// this link will directly open 3rd "story set" in carousel

const Stories: NextPage = (props: any) => {
  const initStories = () => {
    const player = document.body.querySelector("amp-story-player") as any;

    if (player?.isReady) {
      initializeCarousel();
    } else {
      player.addEventListener("ready", () => {
        initializeCarousel();
      });
    }

    function initializeCarousel() {
      const stories = player.getStories();
      const index = parseInt(window.location.hash.charAt(1)) || 0;
      player.show(stories[index].href);
      player.play();
    }
  };
  useEffect(() => initStories(), []);
  const ampStories = `<amp-story-player class="my-player">
  <script type="application/json">
    {
      "behavior": {
        "autoplay": false,
        "pageScroll": false
      },
    }
  </script>
${props.stories.map((s: string) => `<a href="${s}"></a>`).join("")}
</amp-story-player>`;

  return (
    <>
      <Script
        id="amp-player-js"
        async
        src="https://cdn.ampproject.org/amp-story-player-v0.js"
        strategy="beforeInteractive"
      ></Script>
      <link
        href="https://cdn.ampproject.org/amp-story-player-v0.css"
        rel="stylesheet"
        type="text/css"
      ></link>
      <div
        className="lightbox show"
        dangerouslySetInnerHTML={{ __html: ampStories }}
      />
    </>
  );
};

export async function getServerSideProps() {
  const fetchedStoies = [
    "https://wsdemos.uc.r.appspot.com/ampfest/s1",
    "https://wsdemos.uc.r.appspot.com/ampfest/s2",
    "https://wsdemos.uc.r.appspot.com/ampfest/s3",
    "https://wsdemos.uc.r.appspot.com/ampfest/s4",
  ];
  return {
    props: {
      stories: fetchedStoies,
    }, // will be passed to the page component as props
  };
}

export default Stories;

```

###### How to test:

run the app. go to http://localhost:3000#3
it will open a set of web stories as a carousel. The hash path #n (like #3 in this example link), will open the n_th (3rd in this example) story directly.

### Caveat:

even though it works, the stories page is jittery in the initialization. in mobile, it gets stuck sometimes while switching from one story to the next. it is better to store the carousel as pure amp page instead of nextjs page. depends on the decision maker!
