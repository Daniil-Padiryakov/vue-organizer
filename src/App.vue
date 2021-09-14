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

            <div @click="getTodos(day)"
                 v-for="(day, index) in currentMonth.days"
                 :key="index"
                 class="calendar__month-day">
                {{day.day}}
            </div>

        </div>

        <TodoList :todos="currentTodos" @create="createTodo"></TodoList>
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

                currentMonth: null,
                allMonth: [],
                currentTodos: [],
            }
        },
        methods: {

            // вызывается один раз при первом запуске приложения
            initCalendar(year, month) {
                this.currentMonth = this.createNewMonth(year, month)
                localStorage.setItem('month', JSON.stringify(this.currentMonth))
            },

            // вызывается при смене месяца, если месяц уже был создан возвращает его, иначе создает новый
            getSavedMonth(year, month) {
                let savedMonth = this.allMonth.find(item => item.year == year && item.month == month)
                return savedMonth != undefined ? savedMonth : this.createNewMonth(year, month)
            },

            // работа с компонентом TodoList
            getTodos(day) {
                let index = this.currentMonth.days.indexOf(day)
                this.currentTodos = this.currentMonth.days[index].todos
            },
            createTodo(todo) {
                this.currentTodos.push(todo)
                localStorage.setItem('months', JSON.stringify(this.allMonth))
            },

            createNewMonth(year, month) {
                let obj = this.createObjOfMonth(year, month)
                let fromNum = 1
                let toNum = this.getLastDay(year, month)

                let shiftElemsNum = this.getUnshiftElemsNum(year, month)
                let popElemsNum = this.getPushElemsNum(year, month)

                obj = this.createArrOfDays(fromNum, toNum, obj)
                obj = this.shiftElems(shiftElemsNum, obj)
                obj = this.popElems(popElemsNum, obj)

                this.allMonth.push(obj)
                localStorage.setItem('months', JSON.stringify(this.allMonth))
                return obj
            },

            createObjOfMonth(year, month) {
                return {
                    year: year,
                    month: month,
                    days: []
                }
            },

            createArrOfDays(fromNum, toNum, objOfMonth) {
                for (var i = fromNum; i <= toNum; i++) {
                    let obj = {
                        day: i,
                        todos: [],
                    };
                    objOfMonth.days.push(obj)
                }

                return objOfMonth;
            },


            shiftElems(shiftElemsNum, obj) {
                for (let i = 0; i < shiftElemsNum; i++) {
                    obj.days.unshift({});
                }

                return obj;
            },
            popElems(popElemsNum, obj) {
                for (let i = 0; i < popElemsNum; i++) {
                    obj.days.push({});
                }

                return obj;
            },

            getLastDay(year, month) {
                let date = new Date(year, month + 1, 0)
                return date.getDate()
            },

            getUnshiftElemsNum(year, month) {
                let jsDayNum = this.getFirstWeekDayOfMonthNum(year, month)
                let realDayNum = this.getRealDayOfWeekNum(jsDayNum)

                return realDayNum - 1;
            },
            getPushElemsNum(year, month) {
                let jsDayNum = this.getLastWeekDayOfMonthNum(year, month)
                let realDayNum = this.getRealDayOfWeekNum(jsDayNum)

                return 7 - realDayNum;
            },

            getRealDayOfWeekNum(jsNumOfDay) {
                return jsNumOfDay == 0 ? 7 : jsNumOfDay
            },

            getFirstWeekDayOfMonthNum(year, month) {
                let date = new Date(year, month, 1)
                return date.getDay()
            },

            getLastWeekDayOfMonthNum(year, month) {
                let date = new Date(year, month + 1, 0)
                return date.getDay()
            },

            getMonthName(num) {
                let months = [
                    'Янв', 'Фев', 'Мар', 'Апр', 'Май', 'Июн', 'Июль', 'Авг', 'Сен', 'Окт', 'Ноя', 'Дек'
                ]
                return months[num]
            },

            prevMonth() {
                this.year = this.getPrevYear(this.year, this.month)
                this.month = this.getPrevMonth(this.month)

                this.currentMonth = this.getSavedMonth(this.year, this.month)
            },
            nextMonth() {
                this.year = this.getNextYear(this.year, this.month)
                this.month = this.getNextMonth(this.month)

                this.currentMonth = this.getSavedMonth(this.year, this.month)
            },

            getPrevYear(year, month) {
                return month == 0 ? year - 1 : year
            },
            getPrevMonth(month) {
                return month == 0 ? 11 : month - 1
            },

            getNextYear(year, month) {
                return month == 11 ? year + 1 : year
            },
            getNextMonth(month) {
                return month == 11 ? 0 : month + 1
            },

            getDataFromLS(key) {
                let data = localStorage.getItem(key)
                return data != null ? data : this.initCalendar(this.year, this.month)
            }
        },
        created() {
            // this.currentMonth = this.initCalendar(this.year, this.month)
            // console.log(localStorage.getItem('months'))

            console.log(this.getDataFromLS('months'))
            this.currentMonth = JSON.parse(this.getDataFromLS('month'))
            console.log(this.allMonth)

            this.allMonth = JSON.parse(this.getDataFromLS('months'))
        }
    }
</script>

<style>

    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    .calendar {
        min-width: 400px;
        max-width: 700px;
        margin-bottom: 30px;
    }

    .calendar__week {
        display: flex;
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
        flex-wrap: wrap;
    }

    .calendar__month-day {
        width: calc(100% / 7);
        padding: 15px;
        border: teal 1px solid;
        text-align: center;
    }
</style>