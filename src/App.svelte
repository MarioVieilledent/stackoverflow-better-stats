<script lang="ts">
  import { stats } from "./stats";

  const TOP_DESIRED = 30.0;
  const BOTTOM_DESIRED = 5.0;

  const TOP_ADMIRED = 65.0;
  const BOTTOM_ADMIRED = 60.0;

  let TOP_AVG = 45.0;
  let BOTTOM_AVG = 30.0;

  const TOP_UNDERESTIMATED = 65.0;
  const BOTTOM_UNDERESTIMATED = 50.0;

  // Adds averages on index 2
  for (const [lang, vals] of Object.entries(stats)) {
    vals.push((vals[0] + vals[1]) / 2.0);
  }

  // Adds smallest span for high average on index 3
  for (const [lang, vals] of Object.entries(stats)) {
    vals.push(vals[2] / (vals[1] - vals[0]));
  }

  // Adds span on index 4
  for (const [lang, vals] of Object.entries(stats)) {
    vals.push(vals[1] - vals[0]);
  }

  let currSpot = "";
  let currLang = "";

  function mouseEnter(spot, lang) {
    currSpot = spot;
    currLang = lang;
  }
</script>

<main>
  <div class="top">
    <div class="list">
      <h2>Desired</h2>
      <span class="desc">From StackOverflow 2023's survey</span>
      <span class="formula">Raw data</span>
      {#each Object.entries(stats).sort((a, b) => b[1][0] - a[1][0]) as [lang, vals], index}
        <div
          class={"lang" +
            (lang === currLang ? " selected" : "") +
            " tier" +
            Math.floor(index / 10.0 + 1.0)}
          on:mouseenter={() => mouseEnter("Desired", lang)}
          on:mouseleave={() => mouseEnter("", "")}
        >
          <span>{index + 1}&#41;</span>
          <span>{lang}</span>
          <span>{vals[0].toFixed(2)}&#37;</span>
        </div>
      {/each}
    </div>
    <div class="list">
      <h2>Admired</h2>
      <span class="desc">From StackOverflow 2023's survey</span>
      <span class="formula">Raw data</span>
      {#each Object.entries(stats).sort((a, b) => b[1][1] - a[1][1]) as [lang, vals], index}
        <div
          class={"lang" +
            (lang === currLang ? " selected" : "") +
            " tier" +
            Math.floor(index / 10.0 + 1.0)}
          on:mouseenter={() => mouseEnter("Admired", lang)}
          on:mouseleave={() => mouseEnter("", "")}
        >
          <span>{index + 1}&#41;</span>
          <span>{lang}</span>
          <span>{vals[1].toFixed(2)}&#37;</span>
        </div>
      {/each}
    </div>
    <div class="list">
      <h2>Average</h2>
      <span class="desc">Middle point between desired and admired</span>
      <span class="formula">Desired + Admired / 2</span>
      {#each Object.entries(stats).sort((a, b) => b[1][2] - a[1][2]) as [lang, vals], index}
        <div
          class={"lang" +
            (lang === currLang ? " selected" : "") +
            " tier" +
            Math.floor(index / 10.0 + 1.0)}
          on:mouseenter={() => mouseEnter("Average", lang)}
          on:mouseleave={() => mouseEnter("", "")}
        >
          <span>{index + 1}&#41;</span>
          <span>{lang}</span>
          <span>{vals[2].toFixed(2)}&#37;</span>
        </div>
      {/each}
    </div>
    <div class="list">
      <h2>Best</h2>
      <span class="desc">Smallest span, highest average</span>
      <span class="formula">Average / (Admired - Desired)</span>
      {#each Object.entries(stats).sort((a, b) => b[1][3] - a[1][3]) as [lang, vals], index}
        <div
          class={"lang" +
            (lang === currLang ? " selected" : "") +
            " tier" +
            Math.floor(index / 10.0 + 1.0)}
          on:mouseenter={() => mouseEnter("Underestimated", lang)}
          on:mouseleave={() => mouseEnter("", "")}
        >
          <span>{index + 1}&#41;</span>
          <span>{lang}</span>
          <span>{vals[3].toFixed(2)}</span>
        </div>
      {/each}
    </div>
    <div class="list">
      <h2>Underestimated</h2>
      <span class="desc">Largest span</span>
      <span class="formula">Admired - Desired</span>
      {#each Object.entries(stats).sort((a, b) => b[1][4] - a[1][4]) as [lang, vals], index}
        <div
          class={"lang" +
            (lang === currLang ? " selected" : "") +
            " tier" +
            Math.floor(index / 10.0 + 1.0)}
          on:mouseenter={() => mouseEnter("Underestimated", lang)}
          on:mouseleave={() => mouseEnter("", "")}
        >
          <span>{index + 1}&#41;</span>
          <span>{lang}</span>
          <span>{vals[4].toFixed(2)}&#37;</span>
        </div>
      {/each}
    </div>
  </div>
  <div class="bottom">
    <span>Better stats for StackOverflow survey</span>
    <a
      href="https://github.com/MarioVieilledent/stackoverflow-better-stats"
      target="_blank">GitHub repo</a
    >
    <a href="https://survey.stackoverflow.co/2023/" target="_blank">2023 StackOverflow Survey</a>
    <span>JSON of used data:</span>
    <code>{JSON.stringify(stats)}</code>
  </div>
</main>

<style lang="scss">
  main {
    display: flex;
    flex-direction: column;

    .bottom {
      display: flex;
      flex-direction: column;
      width: 100%;
      padding: 12px;
      box-sizing: border-box;

      code {
        padding: 12px;
        color: #aaa;
        font-family: "Courier New", Courier, monospace;
        font-size: 12px;
        box-sizing: border-box;
      }
    }

    .top {
      display: flex;
      margin-bottom: 24px;
      justify-content: center;

      .list {
        display: flex;
        flex-direction: column;
        margin-right: 36px;

        h2 {
          margin: 12px 0px 6px 0px;
        }

        .desc {
          margin-bottom: 6px;
          font-size: 14px;
          white-space: nowrap;
        }

        .formula {
          color: #aaa;
          margin-bottom: 6px;
          font-size: 14px;
          white-space: nowrap;
        }

        .lang {
          display: flex;
          padding-left: 3px;

          span {
            margin-right: 12px;
          }
        }
      }
    }
  }

  .selected {
    background-color: #444;
  }

  .tier1 {
    color: rgb(38, 194, 59);
  }

  .tier2 {
    color: rgb(218, 199, 36);
  }

  .tier3 {
    color: rgb(206, 134, 27);
  }

  .tier4 {
    color: rgb(226, 55, 55);
  }

  .tier5,
  .tier6 {
    color: rgb(202, 41, 175);
  }
</style>
