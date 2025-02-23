<template>
  <nav class="navbar" id="sticky">
    <img id="pic" alt="new logo" src="../assets/pic.png" />

    <div class="brandName">mm29126 finances</div>
    <a href="#" class="toggleButton">
      <span class="bar"></span>
      <span class="bar"></span>
      <span class="bar"></span>
    </a>
    <div class="navbarLinks">
      <ul>
        <li><a href="#">Home</a></li>
        <li><a href="#statisticsTable">Performance</a></li>
        <li><a href="#performanceTable">Portfolio</a></li>
        <li><a href="#transactionsTable">Transactions</a></li>
      </ul>
    </div>
  </nav>
  <div class="site">
    <div class="information">
      <h1>mm29216 finances -<br />your website for your finances</h1>
      <h3>Find out everything about the current status of your finances.</h3>
      <h4>
        View the performance of your stocks, your current portfolio and your
        recent transactions.
      </h4>
    </div>
    <div class="container" id="statisticsTable">
      <div class="info-text">
        <h3 style="text-decoration: underline">
          Performance<img
            id="pictatistics"
            alt="performance logo"
            src="../assets/pictogramPerformance.png"
          />
        </h3>
        <h5>
          Learn how the value of your finances has changed since the
          beginning:<br />
          How much has the value of your inventory shifted?
        </h5>
        <h6>[from {{ startEndDate[0] }} to {{ startEndDate[1] }}]</h6>
      </div>
      <table
        class="table table-bordered border-info table-info table-hover table-striped mt-4 caption-top table-responsive"
        style="--bs-border-opacity: 0.5"
      >
        <caption>
          Performance
        </caption>
        <thead>
          <tr>
            <th scope="col">Begin Value</th>
            <th scope="col">Minimum Value</th>
            <th scope="col">Maximum Value</th>
            <th scope="col">Average Value</th>
            <th scope="col">End Value</th>
          </tr>
        </thead>
        <tbody class="table-group-divider">
          <tr>
            <td>{{ holdingsStatistics[0] }} {{ holdingsStatistics[5] }}</td>
            <td>{{ holdingsStatistics[1] }} {{ holdingsStatistics[5] }}</td>
            <td>{{ holdingsStatistics[2] }} {{ holdingsStatistics[5] }}</td>
            <td>{{ holdingsStatistics[3] }} {{ holdingsStatistics[5] }}</td>
            <td>{{ holdingsStatistics[4] }} {{ holdingsStatistics[5] }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="chartStatisticsContainer">
      <canvas id="chartStats"></canvas>
    </div>
    <div class="container" id="performanceTable">
      <div class="info-text">
        <h3 style="text-decoration: underline">
          Portfolio<img
            id="picportfolio"
            alt="portfolio logo"
            src="../assets/pictogramPortfolio.png"
          />
        </h3>
        <h5>
          Find out at a glance which stocks you are holding at the moment.
        </h5>
        <h6>[This is the status as of {{ startEndDate[1] }}].</h6>
      </div>
      <table
        class="table table-bordered border-info table-light table-hover table-striped mt-4 caption-top table-responsive"
        style="--bs-border-opacity: 0.5"
      >
        <caption>
          Portfolio
        </caption>
        <thead>
          <tr class="align-middle">
            <th scope="col">Asset ID</th>
            <th scope="col">Value</th>
            <th scope="col">Quantity</th>
            <th scope="col">Stock Price</th>
            <th scope="col">Share of Total Volume</th>
          </tr>
        </thead>
        <tbody class="table-group-divider">
          <tr v-for="i in holdings" :key="i.holdingId.POSITION">
            <td>{{ i.holdingId.ASSET_SHORT_NAME }}</td>
            <td>{{ i.value.amount.toFixed(2) }} {{ i.value.currencyId }}</td>
            <td>{{ i.quantity.toFixed(2) }}</td>
            <td>{{ i.quote.amount.toFixed(2) }} {{ i.value.currencyId }}</td>
            <td
              v-if="
                Math.round(
                  (parseFloat(i.value.amount) /
                    parseFloat(holdingsStatistics[4]).toFixed(4)) *
                    1000
                ) < 1
              "
            >
              &lt; 0.05 %
            </td>
            <td v-else>
              {{
                (i.value.amount / holdingsStatistics[4]).toFixed(4).slice(2, 4)
              }}.{{
                (i.value.amount / holdingsStatistics[4]).toFixed(4).slice(4)
              }}
              %
            </td>
          </tr>
          <tr class="table-success">
            <td></td>
            <td class="fat">
              Total Value: {{ holdingsStatistics[4] }}
              {{ holdingsStatistics[5] }}
            </td>
            <td class="fat">Total Quantity: {{ totalQuantityArr[0] }}</td>
            <td></td>
            <td></td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="chartPerformanceContainer">
      <canvas id="chartPerformnc"></canvas>
    </div>
    <div class="container" id="transactionsTable">
      <div class="info-text">
        <h3 style="text-decoration: underline">
          Transactions<img
            id="pictransactions"
            alt="transactions logo"
            src="../assets/pictogramTransactions.png"
          />
        </h3>
        <h6>
          These are all completed transactions of the period from
          {{ startEndDate[0] }} to {{ startEndDate[1] }}:
        </h6>
      </div>
      <table
        class="table table-bordered border-info table-light table-hover table-striped mt-4 caption-top table-responsive"
        style="--bs-border-opacity: 0.5"
      >
        <caption>
          Transactions
        </caption>
        <thead>
          <tr class="align-middle">
            <th scope="col">Transaction Type</th>
            <th scope="col">Date</th>
            <th scope="col">Actual Amount</th>
            <th scope="col">Quantity</th>
            <th scope="col">Price / piece</th>
          </tr>
        </thead>
        <tbody class="table-group-divider">
          <tr
            v-for="i in transactions"
            :key="i.transactionId"
            v-bind:class="
              i.actualAmount.amount > 0 ? 'table-success' : 'table-danger'
            "
          >
            <td v-if="i.transactionDetailedType.includes('Verkauf')">
              {{
                i.transactionDetailedType
                  .split("_")
                  .join(": ")
                  .replace("Verkauf", "Sale")
                  .replace("WP", "SEC")
              }}
            </td>
            <td v-if="i.transactionDetailedType.includes('Kauf')">
              {{
                i.transactionDetailedType
                  .split("_")
                  .join(": ")
                  .replace("Kauf", "Purchase")
                  .replace("WP", "SEC")
              }}
            </td>
            <td v-if="i.transactionDetailedType.includes('Einlage')">
              {{
                i.transactionDetailedType
                  .split("_")
                  .join(": ")
                  .replace("Einlage", "Deposit")
                  .replace("KTO", "ACC")
              }}
            </td>
            <td v-if="i.transactionDetailedType.includes('Entnahme')">
              {{
                i.transactionDetailedType
                  .split("_")
                  .join(": ")
                  .replace("Entnahme", "Withdrawal")
                  .replace("KTO", "ACC")
              }}
            </td>
            <td>{{ i.impactDate.split("-").join("/") }}</td>
            <td>
              {{ i.actualAmount.amount.toFixed(2) }}
              {{ i.actualAmount.currencyId }}
            </td>
            <td>{{ Math.abs(i.quantity) }}</td>
            <td v-if="i.notationType == 'PERCENT'">
              {{ (i.quote.amount / 100).toFixed(2) }} {{ i.quote.currencyId }}
            </td>
            <td v-else>
              {{ i.quote.amount.toFixed(2) }} {{ i.quote.currencyId }}
            </td>
          </tr>
        </tbody>
        <tfoot>
          <td>Legend:</td>
          <td>SEC = Security</td>
          <td>ACC = Account</td>
          <td></td>
          <td></td>
        </tfoot>
      </table>
    </div>
    <div class="chartTransactionsContainer">
      <canvas id="chartTransacts"></canvas>
    </div>
  </div>

  <footer>
    <p>Author: Michael Münzenhofer</p>
    <p>
      <a
        href="mailto:michael.muenzenhofer@stud.th-deg.de"
        class="hover"
        target="_blank"
        style="text-decoration: none"
        >Contact the Author</a
      >
    </p>
    <p>
      <a
        href="https://www.aixigo.com"
        class="hover"
        target="_blank"
        style="text-decoration: none"
        >aixigo</a
      >
    </p>
    <p>
      <a
        class="footera hover"
        href="https://www.aixigo.com/imprint"
        target="_blank"
        style="text-decoration: none"
        >Legal Disclosure</a
      >
      <a
        class="footera hover"
        href="https://www.aixigo.com/responsible-disclosure"
        target="_blank"
        style="text-decoration: none"
        >Responsible Disclosure</a
      >
      <a
        class="footera hover"
        href="https://www.aixigo.com/privacy-policy"
        target="_blank"
        style="text-decoration: none"
        >Privacy Policy</a
      >
    </p>
  </footer>
</template>

<script>
//added import
import Chart from "chart.js/auto";
import { toRaw } from "vue";
import zoomPlugin from "chartjs-plugin-zoom";

Chart.register(zoomPlugin);

export default {
  name: "MyInterface",

  methods: {
    //fetch Data from API: Values := Portfolio
    async fetchValues() {
      const urlValue =
        "https://robowm.aixigo.com:443/analytics/portfolio/values?when=2022-11-17&aggregation=POSITION&contractSet=NONE&contract=DemoContract0001&externalValuations=NONE&useLatestValuations=false&includePendingOrders=false";
      const headersValue = {
        Authorization: "Bearer API_KEY",
      };
      const responseValue = await fetch(urlValue, {
        headers: headersValue,
      });

      const responseJsonValue = await responseValue.json();

      this.holdings = responseJsonValue.holdings;

      //calculation the Total Quantity
      let totalQuantity = 0;
      for (let i = 0; i < responseJsonValue.holdings.length; i++) {
        totalQuantity += parseFloat(responseJsonValue.holdings[i].quantity);
      }
      totalQuantity = totalQuantity.toFixed(2);

      //adding Total Quantity to Array in order to use it in the table
      this.totalQuantityArr[0] = totalQuantity;

      //Array that contains the Asset ID
      for (let i = 0; i < responseJsonValue.holdings.length; i++) {
        this.valueAssetId[i] =
          responseJsonValue.holdings[i].holdingId.ASSET_SHORT_NAME;
      }

      //Array that contains the Values
      for (let i = 0; i < responseJsonValue.holdings.length; i++) {
        this.valueValueData[i] =
          responseJsonValue.holdings[i].value.amount.toFixed(2);
      }
    },

    //fetch Data from API: Statistics:= Performance
    async fetchStatistics() {
      //second API
      const urlStatistics =
        "https://robowm.aixigo.com:443/analytics/portfolio/value-statistics?begin=2015-01-01&end=2019-12-31&restriction=NONE&aggregation=CONTRACT&contractSet=NONE&contract=DemoContract0001&includePendingOrders=false";
      const headersStatistics = {
        Authorization: "Bearer AI_KEY",
      };
      const responseStatistics = await fetch(urlStatistics, {
        headers: headersStatistics,
      });

      const responseJsonStatistics = await responseStatistics.json();

      //declaring Variables for the Performance Table
      const beginValue =
        responseJsonStatistics.holdings[0].valueStatistic.beginValue.amount;
      const endValue =
        responseJsonStatistics.holdings[0].valueStatistic.endValue.amount;
      const minValue =
        responseJsonStatistics.holdings[0].valueStatistic.minValue.amount;
      const maxValue =
        responseJsonStatistics.holdings[0].valueStatistic.maxValue.amount;
      const averageValue =
        responseJsonStatistics.holdings[0].valueStatistic.averageValue.amount;
      const currencyStatistics =
        responseJsonStatistics.holdings[0].accruedInterestStatistic.averageValue
          .currencyId;

      //pushing those Variables into an Array for the Table
      this.holdingsStatistics[0] = beginValue.toFixed(2);
      this.holdingsStatistics[1] = minValue.toFixed(2);
      this.holdingsStatistics[2] = maxValue.toFixed(2);
      this.holdingsStatistics[4] = endValue.toFixed(2);
      this.holdingsStatistics[3] = averageValue.toFixed(2);
      this.holdingsStatistics[5] = currencyStatistics;

      //this Array is for the Chart Data
      this.notAllHoldingsStatistics.push(
        beginValue.toFixed(2),
        minValue.toFixed(2),
        maxValue.toFixed(2),
        endValue.toFixed(2)
      );

      //start and end Date for some statistical Information displayed in text
      let startDate = 0;
      if (urlStatistics.includes("begin")) {
        let startPos = urlStatistics.indexOf("begin");
        startDate = urlStatistics.slice(startPos + 6, startPos + 16);
      }
      startDate = startDate.toString().split("-").join("/");

      let endDate = 0;
      if (urlStatistics.includes("end")) {
        let endPos = urlStatistics.indexOf("end");
        endDate = urlStatistics.slice(endPos + 4, endPos + 14);
      }
      endDate = endDate.toString().split("-").join("/");

      this.startEndDate.push(startDate, endDate);

      for (let i = 0; i < this.notAllHoldingsStatistics.length; i++) {
        this.averageholdingsStatistics[i] = averageValue.toFixed(2);
      }
    },

    //fetch Data from API: transactions
    async fetchTransactions() {
      const urlTransactions =
        "https://robowm.aixigo.com:443/analytics/portfolio/transactions?begin=2015-01-01&end=2019-12-31&restriction=NONE&aggregation=ALL&contractSet=NONE&contract=DemoContract0001&selection=TRANSACTIONS&includePendingOrders=false";

      const headersTransactions = {
        Authorization: "Bearer API_KEY",
      };

      const responseTransactions = await fetch(urlTransactions, {
        headers: headersTransactions,
      });

      const responseJsonTransactions = await responseTransactions.json();

      //Data for Transactions Chart: Transaction Type
      this.transactions = responseJsonTransactions.holdings[0].transactions;

      //Transactions Array with Name Tags
      for (let i = 0; i < this.transactions.length; i++) {
        this.transactionsNameTags[i] =
          responseJsonTransactions.holdings[0].transactions[
            i
          ].transactionDetailedType
            .split("_")
            .join(": ")
            .replace("Verkauf", "Sale")
            .replace("Kauf", "Purchase")
            .replace("Einlage", "Deposit")
            .replace("Entnahme", "Withdrawal")
            .replace("WP", "SEC")
            .replace("KTO", "ACC")
            .slice(5, 15);
      }

      //Transactions Array with the Values
      for (let i = 0; i < this.transactions.length; i++) {
        this.transactionsValuesData[i] =
          responseJsonTransactions.holdings[0].transactions[
            i
          ].actualAmount.amount;
      }
    },
  },

  data() {
    //add Arrays here in order to use Data later
    return {
      holdings: [],
      holdingsStatistics: [],
      transactions: [],
      totalQuantityArr: [],
      averageholdingsStatistics: [],
      notAllHoldingsStatistics: [],
      startEndDate: [],
      transactionsNameTags: [],
      transactionsValuesData: [],
      valueAssetId: [],
      valueValueData: [],
    };
  },

  //functions are triggered in the beginning
  mounted() {
    if (navigator.onLine == false) {
      alert("You must be connected to the Internet to view your finances!");
    }
    this.$nextTick(this.fetchValues);
    this.$nextTick(this.fetchStatistics);
    this.$nextTick(this.fetchTransactions);
    //Charts are rendered after x ms
    setTimeout(function () {
      chartStatistics.update();
    }, 750);
    setTimeout(function () {
      chartTransactions.update();
    }, 1000);
    setTimeout(function () {
      chartPerformance.update();
    }, 1250);

    //Statistics Chart
    let dataProxyStatistics = this.notAllHoldingsStatistics;
    let statisticsData = toRaw(dataProxyStatistics);
    const chartLabel = "Value Statistics";
    const ctxStatistics = document.getElementById("chartStats");
    const chartStatistics = new Chart(ctxStatistics, {
      data: {
        labels: ["Begin Value", "Minimum Value", "Maximum Value", "End Value"],
        datasets: [
          {
            type: "line",
            label: chartLabel,
            data: statisticsData,
            backgroundColor: "rgba(65, 184, 131, 0.2)",
            borderColor: "rgba(65, 184, 131, 1)",
            borderWidth: 3,
            pointRadius: 2,
          },

          {
            type: "line",
            data: this.averageholdingsStatistics,
            label: "Average Value",
            backgroundColor: "rgba(237, 217, 76, 0.2)",
            borderColor: "rgb(237, 217, 76)",
            borderWidth: 2,
            pointRadius: 0,
            borderDash: [15, 10],
          },
        ],
      },
      options: {
        mode: "index",
        scales: {
          y: {
            beginAtZero: true,
          },
        },
      },
    });
    chartStatistics;

    //Performance Chart
    let dataProxyPerformance = this.valueValueData;
    let performanceData = toRaw(dataProxyPerformance);
    const chartLabelPerformance = "Value [EUR]";
    const ctxPerformance = document.getElementById("chartPerformnc");
    const chartPerformance = new Chart(ctxPerformance, {
      data: {
        labels: this.valueAssetId,
        datasets: [
          {
            type: "pie",
            label: chartLabelPerformance,
            data: performanceData,
            backgroundColor: [
              "rgba(255, 99, 132, 0.5)",
              "rgba(54, 162, 235, 0.5)",
              "rgba(255, 206, 86, 0.5)",
              "rgba(75, 192, 192, 0.5)",
            ],
            borderWidth: 0,
            hoverOffset: 4,
          },
        ],
      },
      options: {
        mode: "index",
        spacing: 2,
        layout: {
          padding: {
            bottom: 20,
          },
        },
      },
    });
    chartPerformance;

    //Transactions Chart
    let dataProxyTransactions = this.transactionsValuesData;
    let transactionsData = toRaw(dataProxyTransactions);
    const chartLabelTransactions = "Transactions";
    const ctxTransactions = document.getElementById("chartTransacts");
    const chartTransactions = new Chart(ctxTransactions, {
      data: {
        labels: this.transactionsNameTags,
        datasets: [
          {
            type: "bar",
            label: chartLabelTransactions,
            data: transactionsData,
            backgroundColor: [
              "rgba(255, 99, 132, 0.2)",
              "rgba(54, 162, 235, 0.2)",
              "rgba(255, 206, 86, 0.2)",
              "rgba(75, 192, 192, 0.2)",
            ],
            borderColor: [
              "rgba(255, 99, 132, 1)",
              "rgba(54, 162, 235, 1)",
              "rgba(255, 206, 86, 1)",
              "rgba(75, 192, 192, 1)",
            ],
            borderWidth: 1.5,
          },
        ],
      },
      options: {
        mode: "index",
        scales: {
          y: {
            beginAtZero: true,
          },
        },
        plugins: {
          zoom: {
            zoom: {
              wheel: {
                enabled: true,
              },
            },
            pan: {
              enabled: true,
            },
          },
        },
      },
    });
    chartTransactions;
  },
};
</script>