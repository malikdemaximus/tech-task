<template>
  <div class="flight-card">
    <!-- Left info -->
    <div class="left">
      <div class="main-detail flex">
        <div class="logo">
          <img
            :src="`https://aviata.kz/static/airline-logos/80x80/${flightDetail.carrier}.png`"
            alt
          />
          <span>{{ flightDetail.carrier_name }}</span>
        </div>

        <div class="date-infos flex">
          <div class="date-info">
            <span class="date">{{ date(flightDetailSegment[0].dep_time) }}</span>
            <span class="time">{{ time(flightDetailSegment[0].dep_time) }}</span>
          </div>
          <div class="date-info" v-if="isMobile">
            <span
              class="date"
            >{{ date(flightDetailSegment[flightDetailSegment.length - 1].arr_time) }}</span>
            <span
              class="time"
            >{{ time(flightDetailSegment[flightDetailSegment.length - 1].arr_time) }}</span>
          </div>
        </div>

        <div class="duration-info">
          <div class="flex from-to">
            <span class="from">{{ flightDetailSegment[0].origin_code }}</span>
            <span class="duration">4 ч 20 м</span>
            <span class="to">{{ flightDetailSegment[flightDetailSegment.length - 1].dest_code }}</span>
          </div>
          <img src="../assets/duration-line.svg" alt class="dur-line" />
          <span
            class="transfer"
            v-if="flightDetailSegment.length > 1"
          >через {{ flightDetailSegment[0].dest }}, 1 ч 50 м</span>
        </div>

        <div class="date-info" v-if="!isMobile">
          <span
            class="date"
          >{{ date(flightDetailSegment[flightDetailSegment.length - 1].arr_time) }}</span>
          <span
            class="time"
          >{{ time(flightDetailSegment[flightDetailSegment.length - 1].arr_time) }}</span>
          <!-- <span class="next-day">+1</span> -->
        </div>
      </div>

      <div class="extra-detail flex" v-if="!isMobile">
        <a href="#" class="detail-link">Детали перелета</a>
        <a href="#" class="detail-link">Условия тарифа</a>
        <div class="non-refundable flex" v-if="!flightDetail.refundable">
          <img src="../assets/unreturn.svg" alt />
          <span>невозвратный</span>
        </div>
      </div>
    </div>

    <!-- Right info -->
    <div class="right">
      <p class="flight-price">{{ flight.price }} ₸</p>
      <button class="button">Выбрать</button>
      <span class="extra-text">Цена за всех пассажиров</span>

      <div class="flex luggages">
        <span class="luggage">{{ getLuggageInfo }}</span>
        <a href="#" class="add-luggage" v-if="!isMobile">+ Добавить багаж</a>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: "FlightCard",
  props: {
    flight: {
      type: Object,
      required: true
    }
  },
  computed: {
    isMobile() {
      if (screen.width <= 760) {
        return true
      }
      return false
    },
    flightDetail() {
      return this.flight.itineraries[0][0]
    },
    flightDetailSegment() {
      return this.flight.itineraries[0][0].segments
    },
    getLuggageInfo() {
      if (this.flightDetailSegment[0].baggage_options[0].value === 0) {
        return 'Нет багажа'
      }
      return `${this.flightDetailSegment[0].baggage_options[0].value} ${this.flightDetailSegment[0].baggage_options[0].unit}`
    }
  },
  methods: {
    time(val) {
      let date = val.split(" ")
      let time = date[3]
      return time
    },
    date(val) {
      let date = val.split(" ")
      let day = date[0]
      let month = date[1]
      let weekday = date[2]
      return day + ' ' + month + ' ' + weekday
    }
  },
}
</script>

<style lang="scss" scoped>
.flight-card {
  width: 880px;
  margin-bottom: 12px;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.15);
  border-radius: 4px;
  background-color: #fff;
  display: grid;
  grid-template-columns: 1fr 240px;
  height: fit-content;
}

.left {
  padding: 40px 20px 20px 20px;

  .main-detail {
    gap: 27px;
    align-items: center;
    margin-bottom: 50px;
    margin-left: 23px;

    .logo {
      display: flex;
      align-items: center;
      font-size: 14px;
      font-weight: 600;

      img {
        margin-right: 7px;
        width: 20px;
      }
    }

    .date-info {
      position: relative;
      .date {
        font-size: 12px;
        line-height: 16px;
        display: block;
      }

      .time {
        font-size: 24px;
        font-weight: 600;
        display: block;
      }

      .next-day {
        position: absolute;
        top: 0;
        right: -10px;
        color: #ff3724cc;
        font-size: 10px;
        font-weight: 600;
      }
    }

    .duration-info {
      display: grid;

      .from-to {
        justify-content: space-between;
        align-items: center;
      }
      .from,
      .to {
        font-size: 10px;
        color: #b9b9b9;
      }
      .duration {
        font-size: 14px;
      }

      .transfer {
        color: #ff9900;
        font-size: 12px;
        line-height: 16px;
        text-align: center;
      }
    }
  }

  .extra-detail {
    align-items: center;

    .detail-link {
      margin-left: 23px;
      color: #7284e4;
      font-size: 12px;
      border-bottom: 1px dashed #aab3e3;
    }

    .non-refundable {
      align-items: center;
      margin-left: 46px;

      span {
        color: #707276;
        font-size: 12px;
        margin-left: 8px;
        line-height: 14px;
      }
    }
  }
}

.right {
  background: #f5f5f5;
  border-radius: 0px 4px 4px 0px;
  text-align: center;
  padding: 20px;

  .flight-price {
    font-size: 18px;
    line-height: 20px;
    font-family: "Arial";
    margin-bottom: 13px;
  }

  .button {
    font-size: 18px;
    font-weight: 700;
    line-height: 24px;
    padding: 10px 60px;
    background-color: #55bb06;
    border-radius: 4px;
    margin-bottom: 8px;
    color: #fff;
  }

  .extra-text {
    font-size: 12px;
    line-height: 16px;
    color: #707276;
    text-align: center;
    display: block;
  }

  .luggages {
    justify-content: space-between;
    align-items: center;
    margin-top: 12px;

    .luggage {
      font-size: 12px;
      line-height: 16px;
    }

    .add-luggage {
      background: #eaf0fa;
      border-radius: 2px;
      color: #5763b3;
      padding: 4px 10px;
      font-size: 12px;
      font-weight: 600;
    }
  }
}

@media only screen and (max-width: 992px) {
  .flight-card {
    display: block;
    width: auto;
  }
}

@media only screen and (max-width: 767px) {
  .flight-card {
    position: relative;
    .left {
      padding: 20px;
      .main-detail {
        flex-direction: column;
        align-items: initial;
        margin-bottom: 10px;
        margin-left: 0;

        .date-infos {
          justify-content: space-between;
        }

        .duration-info {
          width: 175px;
          margin: 0 auto;
        }
      }
    }

    .right {
      .luggages {
        margin: 0;
      }
      .luggage {
        position: absolute;
        top: 22px;
        right: 20px;
      }
    }
  }
}
</style>