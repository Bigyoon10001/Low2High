<template>
  <article>
    <section class="section1"> <!--비트코인 양도 소득세 계산기-->
      <h1 class="seo daum-wm-title">{{ getTitle() }}</h1> <!--seo클래스로 안보이게 해놓고 h1으로 제목 데이터만 설정한건가-->
      <div class="section1-title font-35b" @click="titleDropDown = !titleDropDown"> <!--SVG path 그림 그리는거인듯-->
        {{ `${this.slug.length ? this.slug[0] : '비트코인'} 양도소득세 계산기` }}
        <svg :class="{on: titleDropDown}" class="arrow-down" width="22" height="15" viewBox="0 0 22 15" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M11 15L0.607694 -5.0249e-08L21.3923 1.7668e-06L11 15Z" fill="#151515" /> 
        </svg>
      </div> 
      <transition name="dropdown">  <!--dropdown트랜지션 클릭했을때 아래로 링크리스트가 보이는 애니메이션 효과-->
        <div v-if="titleDropDown" class="title-dropdown"> <!--애니메이션 내용-->
          <div class="font-25b current">비트코인 양도소득세 계산기</div>
          <Nuxt-link to="/calc/foreign-stock" class="font-25b">해외주식 양도소득세 계산기</Nuxt-link>
          <Nuxt-link to="/calc/gift-tax" class="font-25b">증여세 계산기</Nuxt-link>
        </div> 
      </transition>
      <div class="section1-sell"> <!--매도 금액 박스-->
        <h2 class="font-25r">1년 동안 총 매도 금액은?</h2>  <!--매도 금액 문구-->
        <div class="input-wrap">  <!--금액 입력창-->
          <input class="font-35b" ref="firstInput" @input="onTypedNumber($event, 1)" type="text" :value="totalSellPay" id="reload" /> <!--숫자 입력 창-->
          <div class="font-30b">원</div>  <!--원-->
        </div>
      </div>
      <div class="section1-buy">  <!--매수 금액 박스-->
        <h2 class="font-25r">매도한 코인을 매수한 금액은?</h2>  <!--매수 금액 문구-->
        <div class="input-wrap">  <!--금액 입력창-->
          <input class="font-35b" @input="onTypedNumber($event, 2)" type="text" :value="buyingPaid" />  <!--숫자 입력 창-->
          <div class="font-30b">원</div>  <!--원-->
        </div>
      </div>
      <button class="section1-calc font-25b" @click="calc">계산하기</button>   <!--계산 버튼-->
    </section>
    <section class="section2">  <!--5월 양도소득세-->
      <div class="section2-wrap"> <!--section2 내부 태두리-->
        <div class="section2-title font-35b">5월에 내야 할 양도소득세는?</div>
        <div class="section2-content">  <!--결과 출력 태두리-->
          <div class="input-wrap">  <!--결과 값-->
            <div class="font-35b">{{ comma(incomeTax) }}</div>
            <div class="font-30b">원</div>
          </div>
          <div @click="dropdownMenu = !dropdownMenu" class="section2-show__detail font-15r">  <!--자세히보기-->
            자세히보기
            <svg :class="{on: dropdownMenu}" class="dropdown-icon" width="11" height="7" viewBox="0 0 11 7" fill="none" xmlns="http://www.w3.org/2000/svg"> <!--자세히보기 ⬇️아이콘-->
              <path d="M5.5 7L0.73686 0.25L10.2631 0.250001L5.5 7Z" fill="white" />
            </svg>
          </div>
          <div v-show="dropdownMenu" class="section2-dropdown"> <!--자세히보기 클릭하면 나오는 내용  v-show: dropdownMenu 값이 참이면 -->
            <div class="section2-dropdown__item">
              <div class="font-13r">1년간 총 매도금액(입력값)</div>
              <div class="font-13r">{{ totalSellPay }}원</div>
            </div>
            <div class="section2-dropdown__item">
              <div class="font-13r">매수금액(입력값)</div>
              <div class="font-13r">{{ buyingPaid }}원</div>
            </div>
            <div class="section2-dropdown__item">
              <div class="font-13r">소득금액(매도금액 - 매수금액)</div>
              <div class="font-13r">{{ comma(incomePay) }}원</div>
            </div>
            <div class="section2-dropdown__item">
              <div class="font-13r">신고 공제액</div>
              <div class="font-13r">{{ comma(deductionPay) }}원</div>
            </div>
            <div class="section2-dropdown__item">
              <div class="font-13r">양도소득세(양도세 20% + 지방세 2%)</div>
              <div class="font-13r">{{ comma(incomeTax) }}원</div>
            </div>
          </div>
        </div>
        <div class="section2-btns">
          <button class="section2-btns__item font-25b" @click="reload">다시 계산하기</button>
          <Nuxt-link to="/expert" class="section2-btns__item df_ac df_jc font-25b">신고 맡기기</Nuxt-link>
        </div>
        <Nuxt-link to="/expert" class="section2-banner">
          <div class="section2-banner__content">
            <div class="font-20r">세무 상담이 필요하신가요?</div>
            <div class="font-20b">택슬리에서 세무사를 만나보세요.</div>
          </div>
          <img class="banner-img" width="223" height="115" src="https://cdn.taxly.kr/assets/2.0/calc/calc-banner.svg" alt="img" />
        </Nuxt-link>
      </div>
    </section>
  </article>
</template>

<script>
import {comma} from '~/js/function'
export default {
  data() {
    return {
      slug: [],
      totalSellPay: '',
      buyingPaid: '',
      comma,
      incomeTax: '',
      incomePay: 0,
      deductionPay: 2500000,
      dropdownMenu: false,
      titleDropDown: false
    }
  },
  created() {
    this.slug = this.$route.params?.slug ? this.$route.params.slug.split('-') : []
  },
  mounted() {
    this.cursorInput()
  },
  methods: {
    cursorInput() {
      this.$refs.firstInput.focus()
    },
    inputBlur() {
      if (this.totalSellPay == 0) {
        this.totalSellPay = 0
      }
    },
    reload() {
      this.totalSellPay = ''
      this.buyingPaid = ''
      this.incomeTax = ''
      this.incomePay = ''
      document.querySelector('#reload').focus()
    },
    getTitle() {
      const title = this.slug.join(' ') ? this.slug.join(' ') : '비트코인 양도소득세 계산기'
      return `${title} | TAXLY.KR`
    },
    getRichTitle() {
      const title = this.slug.join(' ') ? this.slug.join(' ') : '비트코인 양도세'
      return title
    },
    getKeywords() {
      return this.slug.length ? this.slug.join(' ') : '비트코인 양도세 계산'
    },
    getDescription() {
      return (
        this.getKeywords() +
        `비트코인 양도세 계산 비트코인 양도세 계산기 비트코인 양도소득세 계산 비트코인 양도소득세 계산기 비트코인 양도 계산 비트코인 양도 계산기 가상화폐 양도세 계산 가상화폐 양도세 계산기 가상화폐 양도소득세 계산 가상화폐 양도소득세 계산기 가상화폐 양도 계산 가상화폐 양도세 비트코인 양도세 비트코인 과세 가상화폐 과세 NFT 과세 NFT 양도세`
      )
    },
    onTypedNumber(e, type) {
      let value = e.target.value.replace(/[^0-9]/g, '')
      if (value) {
        if (type === 1) {
          this.totalSellPay = this.comma(parseInt(value))
        } else if (type === 2) {
          this.buyingPaid = this.comma(parseInt(value))
        }
        e.target.value = this.comma(parseInt(value))
      } else {
        if (type === 1) {
          this.totalSellPay = ''
        } else if (type === 2) {
          this.buyingPaid = ''
        }
        e.target.value = ''
      }
    },
    calc() {
      const totalSellPay = parseInt(this.totalSellPay.replace(/,/g, ''))
      const buyingPaid = parseInt(this.buyingPaid.replace(/,/g, ''))
      if (buyingPaid >= totalSellPay) {
        alert('소득 금액이 없어서 세금이 없습니다.')
      } else {
        this.incomePay = parseInt(totalSellPay) - parseInt(buyingPaid)
        const incomeTax = (totalSellPay - buyingPaid - 2500000) * 0.22
        this.incomeTax = incomeTax > 0 ? Math.floor(incomeTax) : 0
        alert('계산 완료')
      }
    }
  },
  head() {
    return {
      title: this.getTitle(),
      link: [{rel: 'canonical', href: 'https://taxly.kr' + this.$route.fullPath, id: 'canonical'}],
      meta: [
        //* META : Main
        {
          hid: 'description',
          name: 'description',
          content: this.getDescription(),
          id: 'desc'
        },
        {
          hid: 'keywords',
          name: 'keywords',
          content: this.getKeywords()
        },
        {
          name: 'robots',
          content: 'index, follow'
        },
        //* META : OG
        {
          property: 'og:type',
          content: 'website'
        },
        {
          property: 'og:locale',
          content: 'ko_KR'
        },
        {
          property: 'og:site_name',
          content: 'TAXLY'
        },
        {
          hid: 'ogTitle',
          property: 'og:title',
          content: this.getTitle()
        },
        {
          hid: 'ogUrl',
          property: 'og:url',
          content: 'https://taxly.kr/calc/coin'
        },
        {
          hid: 'ogImage',
          property: 'og:image',
          content: 'https://cdn.taxly.kr/assets/2.0/calc/bitcoin_og.jpg'
        }
      ],
      __dangerouslyDisableSanitizers: ['script'],
      script: [
        {
          innerHTML: `{
            "@context": "https://schema.org",
            "@type": "HowTo",
            "name": "택슬리 코인 세금 계산기를 사용하는 방법",
            "image": {
              "@type": "ImageObject",
              "url": "https://cdn.taxly.kr/assets/2.0/calc/bitcoin_og.jpg",
              "height": "406",
              "width": "305"
            },
            "totalTime": "P2D",
            "supply": [
              {
                "@type": "HowToSupply",
                "name": "약간의 노력과 시간, 계산하고 싶은 의지"
              }
            ],
            "tool": [
              {
                "@type": "HowToTool",
                "name": "${this.getRichTitle()} 계산기"
              }
            ],
            "step": [
              {
                "@type": "HowToStep",
                "url": "https://taxly.kr${this.$route.fullPath}",
                "name": "매수 금액 입력",
                "text": "1년 동안 총 매도한 ${this.getRichTitle()} 금액을 입력합니다.",
                "image": "https://cdn.taxly.kr/assets/2.0/calc/bitcoin_og.jpg"
              },
              {
                "@type": "HowToStep",
                "url": "https://taxly.kr${this.$route.fullPath}",
                "name": "매도 금액 입력",
                "text": "1년 동안 총 매수한 ${this.getRichTitle()} 금액을 입력합니다.",
                "image": "https://cdn.taxly.kr/assets/2.0/calc/bitcoin_og.jpg"
              },
              {
                "@type": "HowToStep",
                "url": "https://taxly.kr/expert",
                "name": "금액 확인 및 신고 도와줄 세무사 찾기",
                "text": "지불해야 하는 양도 소득세와 산출식을 확인합니다. 그 후 신고 업무를 맡길 세무사를 찾으시려면 세무사를 만나보기를 클릭하고 견적서를 작성하기 원한다면 신고 맡기기 버튼을 클릭합니다.",
                "image": "https://cdn.taxly.kr/assets/2.0/calc/bitcoin_og.jpg"
              }
            ]
          }`,
          type: 'application/ld+json'
        }
      ]
    }
  }
}
</script>

<style lang="scss" scoped>
article { // 전체 페이지
  padding-top: 30px;
  margin: 0 auto; // 0: 여백 X // auto: 가운대 정렬
  display: flex;  // Flex 컨테이너 사용
  flex-direction: column; // Flex 방향 = 수직
  align-items: center;  // Flex.item 을 가운대 정렬
  @media #{$newmo} {  // 화면창이 700px이하일 때 패딩 값 조절
    padding-top: 20px;
  }
}
.section1 { // 비트코인 양도 소득세 계산기
  width: 100%;  // 화면 가득 차게!
  margin-bottom: 70px;  // 아래 여백: 70px
  width: 640px; // 293줄에서는 100% 여기는 640px?
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative; // 태그들의 위치를 정함
  @media #{$newmo} {
    width: calc(100% - 40px); // calc함수 사용: 100%에서 40px을 뺀 값
    margin-bottom: 40px;
  }

  .seo {
    display: none;  // 화면상에 안보이게 숨기기
  }

  .input-wrap { // section1: 금액 입력창 
    display: flex;
    align-items: flex-end;
    .font-35b {
      border-bottom: 2px solid #151515;
      margin-right: 12px;
      padding: 0;

      @media #{$newmo} {
        font-size: 25px;
      }
    }
    .font-30b {
      @media #{$newmo} {
        font-size: 25px;
      }
    }
  }
  .section1-title { // 선택한 계산기 이름
    margin-bottom: 50px;
    display: flex;
    align-items: center;
    cursor: pointer;  // 마우스 올렸을 때 포인터 클릭 모양
    .arrow-down { // 계산기 이름 옆에있는 ⬇ 표시
      margin-left: 7px;
      transition: all 0.23s;
      &.on {
        transform: rotate(-180deg);
      }
    }
    @media #{$newmo} {
      font-size: 22px;
      margin-bottom: 30px;
    }
  }
  .title-dropdown { // 클릭 했을때 밑으로 나오는 계산기 종류 리스트
    position: absolute;
    background: #fff;
    border: 1px solid var(--light-gray-color);  // 회색 태두리
    border-radius: 7px; // border 태두리를 둥글게 만듬
    padding: 30px 0;  // 상 우 하 좌 순서 // 두개 적으면 위아래는 30 좌우는 0
    display: flex;
    left: 0;
    right: 0;
    top: 60px;
    align-items: center;
    flex-direction: column;
    width: 640px;
    @media #{$newmo} {
      width: 100%;
      padding: 20px 0;
      top: 40px;
    }
    .font-25b {
      margin-bottom: 20px;
      @media #{$newmo} {
        font-size: 18px;
      }
      &.current {
        color: #ff7e00;
      }
      &:last-child {
        margin-bottom: 0;
      }
    }
  }
  .section1-sell {  // 매수 금액
    margin-bottom: 70px;
    width: 100%;
    @media #{$newmo} {
      margin-bottom: 40px;
    }
    .font-25r {
      margin-bottom: 20px;
      @media #{$newmo} {
        font-size: 17px;
        margin-bottom: 10px;
      }
    }
  }
  .section1-buy { // 매도 금액
    margin-bottom: 60px;
    width: 100%;
    @media #{$newmo} {
      margin-bottom: 30px;
    }
    .font-25r { // 중복
      margin-bottom: 20px;
      @media #{$newmo} {
        font-size: 17px;
        margin-bottom: 10px;
      }
    }
  }
  .section1-calc {  // 계산하기 버튼
    display: flex;
    align-items: center;
    justify-content: center;
    color: #fff;
    background: #ff7e00;
    border-radius: 7px;
    width: 250px;
    height: 50px;
    @media #{$newmo} {
      height: 40px;
      font-size: 17px;
    }
  }
}
.section2 { //5월 양도소득세
  width: 100%;
  padding: 70px 0 50px;
  background-color: #151515;
  @media #{$newmo} {
    padding: 40px 0 30px;
  }
  .section2-wrap {  // section2 내부 태두리
    width: 640px;
    margin: 0 auto;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    @media #{$newmo} {
      width: calc(100% - 40px);
    }
  }
  .section2-title { // 5월에 내야 할 양도소득세는?
    color: #fff;
    margin-bottom: 50px;
    @media #{$newmo} {
      font-size: 22px;
      margin-bottom: 30px;
    }
  }
  .section2-content { // 결과 출력 태두리
    width: 100%;
    margin-bottom: 40px;
    @media #{$newmo} {
      margin-bottom: 30px;
    }
  }
  .input-wrap { // section2: 결과 값
    display: flex;
    align-items: flex-end;
    margin-bottom: 20px;
    .font-35b {
      color: #ff7e00;
      border-bottom: 2px solid #fff;
      margin-right: 12px;
      padding: 0;
      width: 100%;
      @media #{$newmo} {
        font-size: 25px;
      }
    }
    .font-30b {
      color: #fff;
      @media #{$newmo} {
        font-size: 25px;
      }
    }
  }
  .section2-show__detail {  // 자세히보기
    color: #ffffff;
    margin-bottom: 10px;
    cursor: pointer;
    display: inline-flex;
    align-items: center;
    @media #{$newmo} {
      font-size: 13px;
    }
    .dropdown-icon {  // 자세히보기 ⬇️아이콘
      margin-left: 5px;
      margin-bottom: 2px;
      transition: 0.23s;
      &.on {
        transform: rotate(-180deg);
      }
    }
  }
  .section2-dropdown {  // 자세히보기 클릭하면 나오는 내용
    .section2-dropdown__item {  // 내용 하나씩 분류
      border-top: 1px solid rgba(255, 255, 255, 0.5);
      padding: 13px 0 10px; // 상:13 좌우:0 하:10
      display: flex;
      align-items: center;
      justify-content: space-between; // item사이 간격을 균일하게
      @media #{$newmo} {
        padding: 10px 0 10px;
      }
      &:last-child {  // 마지막 item에 적용하는거 아니었어?
        border-bottom: 10px solid rgba(255, 255, 255, 0.5);
        .font-13r {
          color: #ff7e00;
          font-weight: 700;
        }
      }
      .font-13r {
        color: #ffffff;
        @media #{$newmo} {
          font-size: 10px;
        }
      }
    }
  }
  .section2-btns {
    display: flex;
    justify-content: space-between;
    width: 100%;
    margin-bottom: 70px;
    @media #{$newmo} {
      margin-bottom: 40px;
    }
    .section2-btns__item {
      width: 250px;
      height: 50px;
      border-radius: 7px;
      @media #{$newmo} {
        font-size: 17px;
        width: 130px;
        height: 40px;
      }
      &:first-child {
        border: 1px solid #ff7e00;
        color: #ff7e00;
      }
      &:last-child {
        color: #fff;
        background: #ff7e00;
      }
    }
  }
  .section2-banner {
    display: flex;
    justify-content: space-between;
    background: #f8f8f8;
    width: 100%;
    border-radius: 7px;
    padding: 0 20px;
    @media #{$newmo} {
      padding: 17px 20px;
    }
    .section2-banner__content {
      display: flex;
      flex-direction: column;
      justify-content: center;
      .font-20r,
      .font-20b {
        font-size: 17px;
      }
      .font-20r:first-child {
        margin-bottom: 4px;
      }
    }
    .banner-img {
      @media #{$newmo} {
        display: none;
      }
    }
  }
}
</style>
