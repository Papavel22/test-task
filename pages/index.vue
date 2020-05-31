<template>
    <div>
        <div @keyup.space="test" tabindex="0" class="profile">
            <div class="profile-avatar">
                <img src="../static/avatar-test.png" alt="">
            </div>
            <div class="profile-right">
                <div class="profile-header">
                    <h2 class="profile-header-name">
                        {{user.name}}
                    </h2>
                    <span class="profile-header-position">
                 {{user.position}}
             </span>
                    <p class="profile-header-offer">
                        {{user.offer}}
                    </p>
                </div>
                <div class="profile-services">
                    <span class="services-list-title">Услуг</span>
                    <ul
                            class="services-list">
                        <li
                                v-for="item in user.services"
                                class="service">
                        <span class="service-name">
                            <span
                                    class="service-scale"
                                    :class="{side : serviceIsNotMax(item, user.services)}"
                                    v-bind:style="{ width: item.quantity/servicesQuantity(user.services) *100 + '%'}">
                            </span>{{item.name}}</span>
                            <span class="service-quantity">{{item.quantity}}</span>
                        </li>
                    </ul>
                    <div class="profile-services-footer">
                        <strong>Всего</strong>
                        <strong>{{servicesQuantity(user.services)}}</strong>
                    </div>
                </div>
            </div>
        </div>
        <div class="comments">
            <div class="comments-header">
                <div class="comments-header-left">
                    <h2 class="comments-header-title">Последние отзывы</h2>
                    <nuxt-link to="/" class="comments-header-link">Все отзывы</nuxt-link>
                </div>
                <div class="comments-header-right">
                    <span class="likes-quantity">
                        <span class="sprite-icon likes">
                        </span>{{user.likesQuantity}}</span>
                    <span class="comments-quantity"><span
                            class="sprite-icon comments"></span>{{user.commentsQuantity}}</span>
                </div>
            </div>
            <ul class="comments-list">
                <li
                        v-for="comment in user.comments"
                        class="review">
                    <div class="review-header">
                        <span class="review-name">{{comment.name}}</span>
                        <span class="review-date">{{comment.date}}</span>
                    </div>
                    <p class="review-text">{{comment.text}}</p>
                </li>
            </ul>
        </div>
            <div class="comments-form">
            <form @submit.prevent="handleSubmit">
                    <textarea @input="checkForm" @keyup.enter.ctrl="handleSubmit" type="text" class="comments-form-text" v-model="newCommentsText"/>
                    <span class="error">{{ error }}</span>
                <button class="comments-form-btn" type="submit">Написать консультанту</button>
            </form>
            </div>
    </div>

</template>
<script>

export default {
  data () {
    return {
      newCommentsText: '',
      error: '',
      localDate: this.getlocaleDate()
    }
  },

  async asyncData ({ $axios }) {
    let user = {}
    try {
      user = await $axios.$get(`/users/${0}`)
    } catch (e) {
      console.log(e.response)
    }
    return { user }
  },
  methods: {
    async handleSubmit () {
      try {
        if (this.checkForm()) {
          const axios = require('axios')
          const comment = {
            name: 'Иван Иванов',
            text: this.newCommentsText,
            date: this.localDate
          }
          // this.newUser = this.user.comments.push(comment)
          await axios.put(`http://localhost:8080/users/0`, { ...this.user }).then(
            this.user.comments.push(comment)
          )
          // put(`users/${this.users.id}`, { ...this.user })
          this.newCommentsText = ''
        }
      } catch (e) {
        console.log(e)
      }
    },
    getlocaleDate () {
      const months = ['января', 'февраля', 'марта', 'апреля', 'мая', 'июня', 'июля', 'августа', 'сентября', 'октября', 'ноября', 'декабря']
      return `${new Date().getDate()} ${months[new Date().getMonth()]} ${new Date().getFullYear()}`
    },
    serviceIsNotMax (service, services) {
      return services.some(item => item.quantity > service.quantity)
    },
    servicesQuantity (array) {
      let result = 0
      array.map((item) => { result += item.quantity })
      return result
    },
    checkForm () {
      if (this.newCommentsText.length > 3) {
        this.error = ''
      } else {
        this.error = 'Комментарий должен быть длиннее 3-х символов'
        return false
      }
      return true
    }
  }
}
</script>

<style lang="scss" scoped>
    @import './assets/main.scss';

    .profile {
        display: flex;
        padding-bottom: 9px;
        &-avatar {
            margin-right: 7px;
            width: 100%;
            max-width: 125px;
            position: relative;
        }
        &-right {
            width: 100%;
        }
        &-header {
            &-position {
                font-size: 12px;
                line-height: 20px;
                color: #808080;
            }
            &-offer {
                background: #FFFBC8;
                border: 1px solid #DADADA;
                box-sizing: border-box;
                border-radius: 5px;
                margin-left: -32px;
                padding: 10px 8px 10px 30px;
                z-index: 0;
            }
        }
        &-services {
            display: flex;
            justify-content: flex-end;
            flex-direction: column;
            align-items: flex-end;
            padding-top: 10px;
            &-footer {
                display: flex;
                justify-content: space-between;
                width: 100%;
                padding: 10px 40px 10px 0;
            }
        }
        .services-list {
            width: 100%;
            padding: 15px 40px 15px 0;
            border-top: 1px solid #DADADA;
            border-bottom: 1px solid #DADADA;
            &-title {
                font-size: 13px;
                line-height: 15px;
                padding-bottom: 10px;
                margin-right: 34px;
            }
            .service {
                display: flex;
                justify-content: space-between;
                align-items: center;
                position: relative;
                padding: 0 0 0 6px;
                border-left: 1px solid rgba(51, 51, 51, 0.2);
                &:last-child {
                    &-scale {
                        bottom: 0;
                    }
                }
                &-name {
                    font-size: 13px;
                    line-height: 200%;
                    color: #333333;
                }
                &-quantity {
                    font-weight: bold;
                    font-size: 13px;
                    line-height: 15px;
                    color: #333333;
                }
                &-scale {
                    position: absolute;
                    top: 0;
                    bottom: 2px;
                    left: 0;
                    background: #B1E19B;
                    border-radius: 0 3px 3px 0;
                    z-index: -1;
                    &.side {
                        background: #ACE2F8;
                    }
                }
            }
        }
    }

    .comments {
        &-header {
            display: flex;
            justify-content: space-between;
            &-righte, &-left {
                display: flex;
                align-items: flex-end;
            }
            &-right {
                font-size: 12px;
                display: flex;
                span {
                    display: flex;
                    align-items: center;
                }
                .likes-quantity {
                    margin-right: 16px;
                }
                .sprite-icon {
                    &:before {
                        content: "";
                        display: inline-block;
                        margin-right: 3px;
                    }
                    &.likes {
                        &:before {

                            @include sprite($like)
                        }
                    }
                    &.comments {
                        &:before {
                            @include sprite($comment)
                        }
                    }
                }
            }
            &-title {
                font-weight: bold;
                font-size: 16px;
                line-height: 16px;
                padding-right: 10px;
            }
            &-link {
                font-size: 14px;
                line-height: 16px;
                color: #005DA1;
                text-decoration: underline;
            }
        }
        &-list {
            padding-bottom: 22px;
            .review {
                padding-top: 10px;
                &:last-child {
                    margin-bottom: 0;
                }
                &-header {
                    margin-bottom: 10px;
                }
                &-name {
                    font-style: normal;
                    font-weight: bold;
                    font-size: 14px;
                    line-height: 136%;
                    color: #333333;
                }
                &-date {
                    font-size: 12px;
                    line-height: 167%;
                    color: #808080;
                }
                .review-text {
                    position: relative;
                    color: #333333;
                    padding: 15px 18px;
                    font-size: 14px;
                    line-height: 136%;
                    text-align: left;
                    background: #F2FBFF;
                    border: 1px solid #C4CBCF;
                    box-shadow: 0px 4px 10px rgba(128, 128, 128, 0.1);

                    &:before {
                        content: ' ';
                        position: absolute;
                        width: 0;
                        height: 0;
                        left: 24px;
                        top: -10px;
                        border: 5px solid;
                        border-color: transparent transparent #C4CBCF #C4CBCF;
                    }
                    &:after {
                        content: ' ';
                        position: absolute;
                        left: 25px;
                        top: -8px;
                        border: 4px solid;
                        border-color: transparent transparent #f2fbff #f2fbff;
                    }
                }

            }
        }
        &-form {
            padding: 26px 15px 34px 15px;
            background-color: #F2F2F2;
            margin: 0 -20px;
            position: relative;
            form {
                display: flex;
                flex-direction: column;
            }
        ;
            .errors {
                position: absolute;
                bottom: 10px;
            }
            &-text {
                margin-bottom: 23px;
                min-height: 63px;
                width: 100%;
            }
            &-btn {
                font-size: 14px;
                font-weight: bold;
                background-color: #fdd639;
                border: 0;
                padding: 11px;
                border-radius: 23px;
                width: 100%;
                max-width: 281px;
                margin: 0 auto;
            }
        }
    }

</style>
