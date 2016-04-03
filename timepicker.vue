<template>
    <button class="chooseDate">
        <span @click="toggle">{{ value }}</span>
        <div class="dateSelector" v-if="open" transition="fade">
            <div class="wrap year" v-if="yearShow">
                <ul>
                    <li class="btn" @click="yearPre"><i class="fa fa-angle-up"></i></li>
                    <li v-for="item in years" :class="{ active : (year === item) }" @click="chooseYear(item)">{{ item }}</li>
                    <li class="btn" @click="yearNext"><i class="fa fa-angle-down"></i></li>
                </ul>
            </div>
            <div class="wrap month" v-if="monthShow">
                <ul>
                    <li class="btn" @click="toggleToYear">{{ year }}年</li>
                    <li v-for="item in months" :class="{ active: (month === $index + 1) }" @click="chooseMonth($index + 1)">{{ item }}</li>
                </ul>
            </div>
            <div class="wrap date" v-if="dateShow">
                <ul>
                    <li class="btn" @click="toggleToMonth">{{ year }}年 {{ months[month - 1] }}</li>
                    <li class="day">日</li>
                    <li class="day">一</li>
                    <li class="day">二</li>
                    <li class="day">三</li>
                    <li class="day">四</li>
                    <li class="day">五</li>
                    <li class="day">六</li>

                    <li v-for="item in dates" :class="{ other: !item.thismonth, active: (date === item.date) && item.thismonth }" @click="chooseDate(item.date, item.thismonth)">{{ item.date }}</li>
                </ul>
            </div>
            <div class="wrap time" v-if="timeShow">
                <ul>
                    <li class="btn" @click="toggleToDate">{{ year + '年 ' + month + '月' + date + '日' }}</li>
                </ul>
                <input type="tel" placeholder="00" v-model="hour"> : <input type="tel" placeholder="00" v-model="min">
                <div class="button" :class="{ able: isAble }" @click="checkNow">确定</div>
            </div>
            <div style="clear:both;margin-bottom:10px"></div>
        </div>
    </button>
</template>

<script>
export default {
    name: 'timePicker',
    props: {
        microtime: {
            type: Number,
            default: 0
        }
    },
    data () {
        return {
            open: false,
            yearShow: true,
            monthShow: false,
            dateShow: false,
            timeShow: false,
            year: 2016,
            month: 4,
            date: 3,
            hour: '',
            min: '',
            years: [2012, 2013, 2014, 2015, 2016, 2017, 2018, 2019, 2020],
            months: ['一月', '二月', '三月', '四月', '五月', '六月', '七月', '八月', '九月', '十月', '十一月', '十二月'],
            dates: [],
            isAble: false,
            value: '请选择时间'
        }
    },
    methods: {
        checkNow () {
            if (!this.isAble) return
            this.value = [this.year, '0' + this.month, this.format(this.date)].join('.') + ' ' + [this.format(this.hour), this.format(this.min), '00'].join(':')
            this.toggle()
            this.timeShow = false
            this.yearShow = true
            this.microtime = this.timestamp()
        },
        timestamp () {
            let date = new Date(this.year, this.month - 1, this.date, this.hour, this.min, 0)
            return date.getTime() / 1000 // 兼容PHP时间戳
        },
        format (val) {
            return (val.toString().length === 1) ? '0' + val : val
        },
        toggle () {
            this.open = !this.open
        },
        toggleToDate () {
            this.dateShow = true
            this.timeShow = false
        },
        toggleToMonth () {
            this.monthShow = true
            this.dateShow = false
        },
        toggleToYear () {
            this.yearShow = true
            this.monthShow = false
        },
        chooseYear (year) {
            this.year = year
            this.yearShow = false
            this.monthShow = true
        },
        chooseMonth (month) {
            this.month = month
            this.monthShow = false
            this.dateShow = true
        },
        chooseDate (date, thismonth) {
            if (!thismonth) return
            this.date = date
            this.dateShow = false
            this.timeShow = true
        },
        updateDate () {
            let thisyear = this.year
            let thismonth = this.month
            let countThisMonthDays = new Date(thisyear, thismonth, 0).getDate()
            let thisMonthFirstDay = new Date(thisyear, thismonth - 1, 0).getDay() + 1
            thisMonthFirstDay = (thisMonthFirstDay === 7) ? 0 : thisMonthFirstDay
            this.dates = []
            for (let i = 1; i <= countThisMonthDays; i++) {
                let thisDate = {
                    date: i,
                    thismonth: true
                }
                this.dates.push(thisDate)
            }
            this.dates.reverse()
            for (let i = 0; i < thisMonthFirstDay; i++) {
                let thisDate = {
                    date: new Date(thisyear, thismonth - 1, 0).getDate() - i,
                    thismonth: false
                }
                this.dates.push(thisDate)
            }
            this.dates.reverse()
        },
        yearPre () {
            this.years = this.years.map((val, index) => {
                return this.years[0] - 3 + index
            })
        },
        yearNext () {
            this.years = this.years.map((val, index) => {
                return this.years[0] + 3 + index
            })
        }
    },
    ready () {
        let date = new Date()
        this.year = date.getFullYear()
        this.month = date.getMonth() + 1
        let thisyear = this.year
        this.years = this.years.map((val, index) => {
            return thisyear - 4 + index
        })
        this.updateDate()
    },
    watch: {
        'year': function () {
            this.updateDate()
        },
        'month': function () {
            this.updateDate()
        }
    },
    computed: {
        isAble () {
            return (this.year && this.month && this.date && this.hour.length <= 2 && this.min.length <= 2 && !isNaN(parseInt(this.hour)) && this.hour < 24 && this.hour >= 0 && this.min < 60 && this.min >= 0 && !isNaN(parseInt(this.min)))
        }
    }
}
</script>

<style lang="less" scoped>
.chooseDate {
    width: 100%;
    height: 32px;
    margin-top: 5px;
    color: #555;
    font-size: 14px;
    line-height: 32px;
    background: #F6F6F6;
    border-radius: 3px;
    transition: all 200ms ease;

    .dateSelector {
        position: relative;
        top: 8px;
        left: 50%;
        margin-left: -150px;
        width: 300px;
        background: #FFF;
        border: 1px solid #ddd;
        box-shadow: 0 3px 3px rgba(0, 0, 0, .1);
        border-radius: 3px;

        .wrap {
            width: 100%;
            height: 100%;
            padding: 0 14px;
            
            ul {
                li {
                    float: left;
                    width: 90px;
                    margin-top: 2px;
                    list-style: none;
                    text-align: center;
                    cursor: pointer;
                    display: block;
                    border-radius: 2px;

                    &:hover {
                        background: rgba(0, 0, 0, .05)
                    }

                    &:active {
                        background: rgba(0, 0, 0, .1)
                    }
                }

                .btn {
                    width: 100%;
                }

                .day {
                    cursor: default;

                    &:hover {
                        background: none
                    }

                    &:active {
                        background: none
                    }
                }


                .active {
                    color: #FFF;
                    background: #F45757;

                    &:hover {
                        background: #F45757;
                    }

                    &:active {
                        background: #d44343;
                    }
                }
            }
        }

        .date {
            padding: 0 9px;

            ul {
                li {
                    width: 40px;
                }

                .other {
                    color: #bbb;
                    cursor: default;

                    &:hover {
                        background: none;
                    }

                    &:active {
                        background: none;
                    }
                }
            }
        }

        .time {
            color: #F45757;
            font-size: 16px;

            input {
                width: 60px;
                height: 40px;
                font-size: 24px;
                color: #F45757;
                text-align: center;
                border-bottom: 2px solid #ddd;

                &:focus {
                    border-bottom: 2px solid #F45757;
                }

                &::-webkit-input-placeholder {
                    color: #ddd;
                }
            }

            .button {
                width: 100px;
                height: 32px;
                margin: 0 auto;
                margin-top: 10px;
                color: #aaa;
                font-size: 14px;
                line-height: 32px;
                background: #F6F6F6;
                border-radius: 3px;
                transition: all 200ms ease;
            }

            .able {
                color: #FFF;
                background: #F45757;
                cursor: pointer;

                &:active {
                    background: #d44343;
                }
            }
        }

        &:before {
            position: relative;
            top: -6px;
            left: 50%;
            margin-left: -5px;
            content: "";
            width: 10px;
            height: 10px;
            border: 1px solid #ddd;
            border-right: none;
            border-bottom: none;
            background: #FFF;
            display: block;
            transform: rotate(45deg);
        }
    }
}

/* 必需 */
.fade-transition {
    transition: all 400ms ease;
    transform-origin: 0 0;
    transform: translateY(0);
    opacity: 1;
}

/* .fade-enter 定义进入的开始状态 */
/* .fade-leave 定义离开的结束状态 */
.fade-enter, .fade-leave {
    transform: translateY(-20px);
    opacity: 0;
}
</style>