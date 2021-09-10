<template>

    <div class="calendar">

        <div class="calendar__info">
            <span>{{this.getMonthName(this.month) + ' ' + this.year}}</span>
        </div>

        <div class="calendar__nav">
            <button class="btn btn-outline-success" @click="prevMonth" type="button">Prev</button>
            <button class="btn btn-outline-success" @click="nextMonth" type="button">Next</button>
        </div>

        <div class="calendar__week">
            <div class="calendar__week-day">Пн</div>
            <div class="calendar__week-day">Вт</div>
            <div class="calendar__week-day">Ср</div>
            <div class="calendar__week-day">Чт</div>
            <div class="calendar__week-day">Пт</div>
            <div class="calendar__week-day">Сб</div>
            <div class="calendar__week-day">Вс</div>
        </div>

        <div class="calendar__month">

            <div v-for="day in testDays" class="calendar__month-day">
                {{day}}
            </div>

        </div>

    </div>

</template>

<script>
    import TodoList from '@/components/TodoList.vue'
    import CalendarCell from '@/components/CalendarCell.vue'

    export default {
        components: {
            CalendarCell, TodoList
        },
        data() {
            return {
                year: 2021,
                month: 8,

                testDays: null,
                test: null,
            }
        },
        methods: {
            initCalendar(year, month) {
                return this.createResultArr(year, month)
            },
            createResultArr(year, month) {
                let arr = []
                let fromNum = 1
                let toNum = this.getLastDay(year, month)

                let shiftElemsNum = this.getUnshiftElemsNum(year, month)
                let popElemsNum = this.getPushElemsNum(year, month)

                arr = this.createArrOfDays(fromNum, toNum)
                arr = this.shiftElems(shiftElemsNum, arr)
                arr = this.popElems(popElemsNum, arr)

                return arr
            },

            createArrOfDays(fromNum, toNum) {
                let arr = [];
                for (var i = fromNum; i <= toNum; i++) {
                    arr.push(i);
                }

                return arr;
            },

            shiftElems(shiftElemsNum, arr) {
                for (let i = 0; i < shiftElemsNum; i++) {
                    arr.unshift('');
                }

                return arr;
            },
            popElems(popElemsNum, arr) {
                for (let i = 0; i < popElemsNum; i++) {
                    arr.push('');
                }

                return arr;
            },

            getLastDay(year, month) {
                let date = new Date(year, month + 1, 0)
                return date.getDate()
            },

            getUnshiftElemsNum(year, month) {
                let jsDayNum = this.getFirstWeekDayOfMonthNum(year, month);
                let realDayNum = this.getRealDayOfWeekNum(jsDayNum);

                return realDayNum - 1;
            },
            getPushElemsNum(year, month) {
                let jsDayNum = this.getLastWeekDayOfMonthNum(year, month);
                let realDayNum = this.getRealDayOfWeekNum(jsDayNum);

                return 7 - realDayNum;
            },

            getRealDayOfWeekNum(jsNumOfDay) {
                if (jsNumOfDay == 0) {
                    return 7;
                } else {
                    return jsNumOfDay;
                }
            },

            getFirstWeekDayOfMonthNum(year, month) {
                let date = new Date(year, month, 1);
                return date.getDay();
            },

            getLastWeekDayOfMonthNum(year, month) {
                let date = new Date(year, month + 1, 0);
                return date.getDay();
            },

            getMonthName(num) {
                let monthes = [
                    'Янв', 'Фев', 'Мар', 'Апр', 'Май', 'Июн',
                    'Июль', 'Авг', 'Сен', 'Окт', 'Ноя', 'Дек'
                ];

                return monthes[num];
            },

            prevMonth () {
                this.year = this.getPrevYear(this.year, this.month)
                this.month = this.getPrevMonth(this.month)

                this.testDays = this.initCalendar(this.year, this.month)
            },
            nextMonth () {
                this.year = this.getNextYear(this.year, this.month)
                this.month = this.getNextMonth(this.month)

                this.testDays = this.initCalendar(this.year, this.month)
            },

            getPrevYear(year, month) {
                if (month == 0) {
                    return year - 1;
                } else {
                    return year;
                }
            },
            getPrevMonth(month) {
                if (month == 0) {
                    return 11;
                } else {
                    return month - 1;
                }
            },

            getNextYear(year, month) {
                if (month == 11) {
                    return year + 1;
                } else {
                    return year;
                }
            },
            getNextMonth(month) {
                if (month == 11) {
                    return 0;
                } else {
                    return month + 1;
                }
            },
        },
        created() {
            this.testDays = this.initCalendar(this.year, this.month)
        }
    }
</script>

<style >

    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    .calendar {

    }

    .calendar__week {
        display: flex;
        /*max-width: 800px;*/
        justify-content: space-between;
    }

    .calendar__week-day {
        padding: 15px;
        border: teal 1px solid;
        width: 100%;
        text-align: center;
    }

    .calendar__month {
        display: flex;
        /*max-width: 900px;*/
        flex-wrap: wrap;
    }

    .calendar__month-day {
        width: calc(100% / 7);
        padding: 15px;
        border: teal 1px solid;
        text-align: center;
    }
</style>