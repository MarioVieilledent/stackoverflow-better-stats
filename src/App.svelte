<script lang="ts">
  let stats: { [key: string]: { [key: string]: number[] } };

  fetch("./stats.json")
    .then((res) => res.json())
    .then((data) => {
      stats = data;
      // Calculate stats for all graphs
      Object.values(stats).forEach((graph) => calcStats(graph as any));
    });

  let display = [
    {
      title: "Desired",
      desc: "From StackOverflow 2023's survey",
      formula: "% of respondents who want to use it",
      indexInArray: 0,
    },
    /*
    {
      title: "Average focus on desired",
      desc: "First quartile",
      formula: "(3*Desired + 1*Admired) / 4",
      indexInArray: 2,
    },
    */
    {
      title: "Average",
      desc: "Point in between Desired and Admired",
      formula: "(Desired + Admired) / 2",
      indexInArray: 3,
    },
    /*
    {
      title: "Average focus on admired",
      desc: "Third quartile",
      formula: "(1*Desired + 3*Admired) / 4",
      indexInArray: 4,
    },
    */
    {
      title: "Admired",
      desc: "From StackOverflow 2023's survey",
      formula: "% of respondents who want to continue using it",
      indexInArray: 1,
    },
    {
      title: "Underestimated",
      desc: "Width between Desired and Admired",
      formula: "Admired - Desired",
      indexInArray: 5,
    },
  ];

  // Calculate stats for one graph
  function calcStats(graph: { [key: string]: number[] }): void {
    for (let vals of Object.values(graph)) {
      vals.push(
        (vals[0] * 3 + vals[1]) / 4.0, // Average with focus on Desired
        (vals[0] + vals[1]) / 2.0, // Average
        (vals[0] + vals[1] * 3) / 4.0, // Average with focus on Admired
        vals[1] - vals[0]
      ); // Underestimation)
    }
  }

  let currSpot = "";
  let currLang = "";

  function mouseEnter(spot: string, lang: string): void {
    currSpot = spot;
    currLang = lang;
  }
</script>

<main>
  <div class="top">
    {#if stats}
      {#each Object.entries(stats) as [graphName, graphStats]}
        {@const numberOfItemInTier = Object.keys(graphStats).length / 5.0}
        <h2>{graphName}</h2>
        <div class="graph">
          {#each display as listToDisplay}
            <div class="list">
              <h2>{listToDisplay.title}</h2>
              <span class="desc">{listToDisplay.desc}</span>
              <span class="formula">{listToDisplay.formula}</span>
              {#each Object.entries(graphStats).sort((a, b) => b[1][listToDisplay.indexInArray] - a[1][listToDisplay.indexInArray]) as [lang, vals], index}
                <div
                  class={"lang" +
                    (lang === currLang ? " selected" : "") +
                    " tier" +
                    Math.floor(index / numberOfItemInTier + 1.0)}
                  on:mouseenter={() => mouseEnter("Desired", lang)}
                  on:mouseleave={() => mouseEnter("", "")}
                >
                  <span>{index + 1}&#41;</span>
                  <span>{lang}</span>
                  <span>{vals[listToDisplay.indexInArray].toFixed(2)}&#37;</span
                  >
                </div>
              {/each}
            </div>
          {/each}
        </div>
      {/each}
    {/if}
  </div>
  <div class="bottom">
    <span>Better stats for StackOverflow survey</span>
    <a
      href="https://github.com/MarioVieilledent/stackoverflow-better-stats"
      target="_blank">GitHub repo</a
    >
    <a href="https://survey.stackoverflow.co/2023/" target="_blank"
      >2023 StackOverflow Survey</a
    >
    <span>JSON of used data:</span>
    {#if stats}
      <code>{JSON.stringify(stats)}</code>
    {/if}
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
      flex-direction: column;
      margin-bottom: 24px;
      justify-content: center;
      align-items: center;

      .graph {
        display: flex;
        margin-bottom: 24px;
        justify-content: center;

        .list {
          display: flex;
          flex-direction: column;
          margin-right: 36px;

          h2 {
            margin: 12px 0px 6px 0px;
            font-size: 20px;
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
              font-size: 14px;
            }
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
