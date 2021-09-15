<template>

    <div class="app">

        <div class="calendar">

            <div class="calendar__nav">

                <button class="btn btn-outline-success nav" @click="prevMonth" type="button">Prev</button>

                <div class="calendar__info">
                    <span>{{this.getMonthName(this.month) + ' ' + this.year}}</span>
                </div>

                <button class="btn btn-outline-success nav" @click="nextMonth" type="button">Next</button>

            </div>

            <table class="calendar__month">
                <thead>
                <tr class="calendar__week">
                    <th class="calendar__week-day">Mo</th>
                    <th class="calendar__week-day">Tu</th>
                    <th class="calendar__week-day">We</th>
                    <th class="calendar__week-day">Th</th>
                    <th class="calendar__week-day">Fr</th>
                    <th class="calendar__week-day">Sa</th>
                    <th class="calendar__week-day">Su</th>
                </tr>
                </thead>
                <tbody class="body">
                <tr v-for="(days, indexDays) in currentMonth.days" :key="indexDays">
                    <td @click="getTodos(day)"
                        v-for="(day, index) in days"
                        :key="index"
                        class="calendar__month-day"

                        :class="{active: activeId == day.day & activeId != undefined}">
                        {{day.day}}
                    </td>
                </tr>
                </tbody>

            </table>

            <TodoList v-if="activeId > 0" :todos="currentTodos" @create="createTodo"></TodoList>

        </div>

    </div>

</template>

<script>
    import TodoList from '@/components/TodoList.vue'

    export default {
        components: {
            TodoList
        },
        data() {
            return {
                year: 2021,
                month: 8,

                currentMonth: null,
                allMonth: [],
                currentTodos: [],
                isActive: false,
                activeId: -1,
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
                for (let subArr of this.currentMonth.days) {
                    for (let elem of subArr) {
                        if (day == elem) {
                            this.currentTodos = elem.todos
                        }
                    }
                }

                if (this.activeId === day.day) {
                    this.activeId = -1;
                } else {
                    this.activeId = day.day
                }
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
                obj.days = this.chunk(obj.days, 7)

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
                        isActive: false,
                    };
                    objOfMonth.days.push(obj)
                }

                return objOfMonth;
            },

            chunk(arr, n) {
                let result = [];
                let count = Math.ceil(arr.length / n);

                for (let i = 0; i < count; i++) {
                    let elems = arr.splice(0, n);
                    result.push(elems);
                }

                return result;
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
                    'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'
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
                return data
            }
        },
        created() {
            if (this.getDataFromLS('month') == null) {
                this.initCalendar(this.year, this.month)
            } else {
                this.allMonth = JSON.parse(this.getDataFromLS('months'))
                this.currentMonth = this.getSavedMonth(this.year, this.month)
            }
        }
    }
</script>

<style>

    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    body {
        background-color: #86D1E4;
    }

    .app {
        display: flex;
        justify-content: center;
        background-color: white;
        border-radius: 10px;
    }

    .calendar {
        min-width: 400px;
        max-width: 700px;
        margin-bottom: 30px;

    }

    .calendar__week-day {
        padding: 15px;
        text-align: center;
        border-bottom: 1px lightgray solid;
    }

    table {
        font-size: 18px;
        margin: 0 auto;
        border-collapse: separate;
        border-spacing: 0 5px;
        margin-bottom: 20px;
    }

    .calendar__month-day {
        padding: 12px;
        text-align: center;
        margin: 2px;
        font-weight: 600;
    }

    .calendar__nav {
        display: flex;
        justify-content: center;
        margin-top: 20px;
        margin-bottom: 5px;
    }

    .calendar__info {
        padding: 5px 25px;
        font-weight: 700;
        margin: 0 40px;
        background-color: #ECEDEF;
        border-radius: 7px;
        font-size: 18px;
    }

    th {
        color: #E972AD;
    }

    .active {
        background-color: #F19DCB;
        color: white;
        border-radius: 10px;
    }

    .nav {
        color: white;
        background-color: #E972AD;
        border: none;
        padding: 8px 15px;
        font-weight: 700;
    }
    
    .nav:hover {
        background-color: #d65c97;
    }

</style>