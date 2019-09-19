<template>
    <div>
        <Row type="flex" justify="space-between" class="operation-bar">
            <Col><p @click="previousEvent">上一月</p></Col>
            <Col><p>{{dateStr}}</p></Col>
            <Col><p @click="nextEvent">下一月</p></Col>
        </Row>
        <!--星期-->
        <Row type="flex">
            <Col class="text-center td-border date-header">日</Col>
            <Col class="text-center td-border date-header">一</Col>
            <Col class="text-center td-border date-header">二</Col>
            <Col class="text-center td-border date-header">三</Col>
            <Col class="text-center td-border date-header">四</Col>
            <Col class="text-center td-border date-header">五</Col>
            <Col class="text-center td-border date-header">六</Col>
        </Row>
        <!--日期-->
        <Row type="flex">
            <Col v-for="(item, index) in allDays" class="text-center td-border">
                <p :class="item.noToMonth ? 'no-to-month' : 'is-to-month'">{{item.dayVal}}{{item.isToMonth}}</p>
            </Col>
        </Row>
    </div>
</template>
<script>
    export default {
        name: 'sh-date-picker',
        data () {
            return {
                dateStr: '',
                allDays: [],
                dayWeek: 0,
                monthDays: 0
            };
        },
        methods: {
            getPreMonth (date) {
                let arr = date.split('/');
                let year = arr[0]; // 获取当前日期的年份
                let month = arr[1]; // 获取当前日期的月份
                let day = arr[2]; // 获取当前日期的日
                let days = new Date(year, month, 0);
                days = days.getDate(); // 获取当前日期中月的天数
                let year2 = year;
                let month2 = parseInt(month) - 1;
                if (month2 === 0) {
                    year2 = parseInt(year2) - 1;
                    month2 = 12;
                }
                if (month2 < 10) {
                    month2 = '0' + month2;
                }
                let t2 = year2 + '-' + month2;
                return t2;
            },
            getNextMonth (date) {
                let arr = date.split('/');
                let year = arr[0]; // 获取当前日期的年份
                let month = arr[1]; // 获取当前日期的月份
                let day = arr[2]; // 获取当前日期的日
                let days = new Date(year, month, 0);
                days = days.getDate(); // 获取当前日期中的月的天数
                let year2 = year;
                let month2 = parseInt(month) + 1;
                if (month2 === 13) {
                    year2 = parseInt(year2) + 1;
                    month2 = 1;
                }
                if (month2 < 10) {
                    month2 = '0' + month2;
                }
                let t2 = year2 + '-' + month2;
                return t2;
            },
            // 上一月
            previousEvent () {
                this.dateStr = this.getPreMonth(this.dateStr);
                this.calDate();
            },
            // 下一月
            nextEvent () {
                this.dateStr = this.getNextMonth(this.dateStr);
                this.calDate();
            },
            calDate () {
                this.allDays = [];
                // 获取当前年、月、日
                let d = this.dateStr ? new Date(this.dateStr) : new Date();
                let year = d.getFullYear();
                let month = d.getMonth() + 1;
                this.dateStr = `${year}/${month}`;
                // 获取当前月第一天是周几
                this.dayWeek = new Date(year, month - 1, 1).getDay();
                // 填补每月一号前面的空白
                let firstNum = 7 - this.dayWeek;
                for (let i = 0; i < 7 - firstNum; i++) {
                    this.allDays.unshift({
                        dayVal: this.getDay(`${year}-${month}-1`, -i).dayVal,
                        dateVal: this.getDay(`${year}-${month}-1`, -i).dateVal,
                        noToMonth: true
                    });
                }
                // 获取当前月的天数
                this.monthDays = new Date(year, month, 0).getDate();
                // 实际天数
                if (this.monthDays) {
                    for (let j = 0; j < this.monthDays; j++) {
                        this.allDays.push({
                            timeVal: `${year}/${month}/${j + 1}`,
                            dayVal: j + 1
                        });
                    }
                };
                // 获取月最后一天是周几
                this.lastDayWeek = new Date(year, month - 1, this.monthDays).getDay();
                // 填补每月最后一天的空白
                let lastNum = 7 - this.lastDayWeek - 1;
                for (let k = 1; k <= lastNum; k++) {
                    this.allDays.push({
                        dayVal: this.getDay(`${year}-${month}-${this.monthDays}`, k).dayVal,
                        dateVal: this.getDay(`${year}-${month}-${this.monthDays}`, k).dateVal,
                        noToMonth: true
                    });
                }
            },
            // 获取前后N天的日期（day:往前传负数，往后传正数）
            getDay (date, day) {
                let today = new Date(date);
                let targetday_milliseconds = today.getTime() + 1000 * 60 * 60 * 24 * day;
                today.setTime(targetday_milliseconds);
                let tYear = today.getFullYear();
                let tMonth = today.getMonth();
                let tDate = today.getDate();
                return {
                    dateVal: tYear + '-' + tMonth + '-' + tDate,
                    dayVal: tDate
                };
            }
        },
        mounted () {
            this.calDate();
        }
    };
</script>
<style lang="less">
    @font_color: #fff;
    @font_size: 18px;
    .td-border {
        width: 14.28571428571429%;
        /*width: 40px;*/
        border: solid 1px gray;
        border-right: none;
        line-height: 36px;
        background: orange;
        color: @font_color;
        font-size: 16px;
    }
    .td-border:last-child {
        border-right: solid 1px gray;
    }
    .date-header {
        font-size: 28px;
        font-weight: bold;
        line-height: 42px;
        background: deepskyblue;
    }
    .operation-bar {
        width: 100%;
        background: deeppink;
        color: @font_color;
        font-size: @font_size;
        font-weight: bold;
    }
    .no-to-month {
        color: gray;
    }
    .is-to-month {
        color: @font_color;
    }
</style>
