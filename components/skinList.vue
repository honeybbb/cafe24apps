<template>
  <div class="skinListTitle">
    <div class="skinListName">
      <h2 class="title">스킨 목록</h2>
      <ul class="list">
        <li
            v-for="skin in skinList"
            :class="scode==skin.s_code?'active':''"
        >
          <a class="skinName" @click="onSkinName(skin.s_code)">
            [{{skin.type}}] {{ skin.s_name }}
            <span>({{ skin.s_code }})</span>
            <v-icon small @click="DeleteSkinList(skin.s_code)">mdi-delete-outline</v-icon>
          </a>

        </li>
      </ul>
    </div>
    <a @click="dialog=true"  class="skinNameBtn">스킨추가</a>

    <v-dialog v-model="dialog" width="auto">
      <v-card color="#fff">
        <div id="modalSkinContainer" class="modal" style="opacity: 1; display: inline-block;">
          <div id="modalSkinList">
            <div class="tlt_section">
              <div class="tlt">스킨추가</div>
              <div class="desc">추가할 스킨을 선택하세요</div>
            </div>

            <div class="list_section">
              <!--ul class="list_tab">
                <li class=""><a @click="" data-tab=".select">스킨 선택</a></li>
                <li class="active"><a href="" data-tab=".add">스킨 추가</a></li>
              </ul-->

              <!--div class="tab-conts select">
                <ul class="webpublic-banner list_skin select-skin">

                  <li class="webpublic-banner-item" data-name="[PC] by당당걸" data-code="skin3">
                    <div class="thumb"><img src="https://dcenter-img.cafe24.com/d/product/2024/01/02/16cfc2f4daafa6416b4c222e69f40ce4.jpg" alt=""></div>
                    <div class="name">[PC] by당당걸<span class="no">skin3</span></div>
                    <div class="lang">ko_KR</div>
                  </li>
                  <li class="webpublic-banner-item" data-name="[PC] by육육" data-code="skin1">
                  <div class="thumb"><img src="https://dcenter-img.cafe24.com/d/product/2017/05/23/1581458.jpg" alt=""></div>
                  <div class="name">[PC] by육육<span class="no">skin1</span></div>
                  <div class="lang">ko_KR</div>
                </li>
                </ul>
                <div class="btn_section">
                  <a href="javascript:;" class="btn-basic btn-select-skin btn-big">스킨 선택하기</a>
                </div>
              </div-->

              <div class="tab-conts add active">
                <ul class="webpublic-banner list_skin add-skin">
                  <li
                      v-for="skin in themes"
                      class="webpublic-banner-item"
                      :data-name="skin.skin_name"
                      :data-code="skin.skin_code"
                      @click="selected=skin"
                      :class="selected==skin?'selected':''"
                  >
                    <div class="thumb"><img src="https://dcenter-img.cafe24.com/d/product/2017/05/23/1581458.jpg" alt=""></div>
                    <div class="name">[PC] {{ skin.skin_name }}</div>
                    <div class="lang">{{ skin.language_code }}</div>
                  </li>
                  <li
                      v-for="skin in mo_themes"
                      class="webpublic-banner-item"
                      :data-name="skin.skin_name"
                      :data-code="skin.skin_code"
                      @click="selected=skin"
                      :class="selected==skin?'selected':''"
                  >
                    <div class="thumb"><img src="https://dcenter-img.cafe24.com/d/product/2017/05/23/1581458.jpg" alt=""></div>
                    <div class="name">[M] {{ skin.skin_name }}</div>
                    <div class="lang">{{ skin.language_code }}</div>
                  </li>
                </ul>
                <div class="btn_section">
                  <a @click="addSkinList" class="btn-basic btn-add-skin btn-big">스킨 추가하기</a>
                </div>
              </div>
            </div>
          </div>
          <!--a href="#close-modal" rel="modal:close" class="close-modal ">Close</a-->
        </div>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: {
    themes: Array,
    mo_themes: Array
  },
  data () {
    return {
      skinList: [],
      scode: '',
      dialog: false,
      bannerList: [],
      selected: [],
    }
  },
  methods: {
    DeleteSkinList(scode) {
      let result = confirm("스킨을 정말로 삭제하시겠습니까?")
      let params = {
        'mallId' : "renewwave",
        'scode' : scode
      }

      if(result) {
        axios.delete(`http://localhost:3009/api/v1/theme/list/delete`, { params })
          .then(res => {
            console.log(res.data.data)
            this.getSkinList()
          })
      }
    },
    onSkinName(scode) {
      this.scode = scode
      this.$emit('on-skin-name', this.scode)
      this.getBannerGroup(scode)
    },
    addSkinList() {
      // skin_type 결정
      const type = this.selected.skin_code.includes('mobile') ? "M" : "PC";

      let params = new URLSearchParams();
      params.append('mallId', "renewwave")
      params.append('skinName', this.selected.skin_name)
      params.append('skinCode', this.selected.skin_code)
      params.append('type', type)

      // 중복 체크 API 호출
      axios.post(`http://localhost:3009/api/v1/theme/list/check`, params)
          .then(res => {
            let result = res.data.data;
            // 존재하는 경우
            if (result.length > 0) {
              alert("이미 존재하는 스킨입니다.");
              return;
            } else {
              // 새로운 스킨 추가
              return axios.post(`http://localhost:3009/api/v1/theme/list/add`, params);
            }
          })
          .then(res => {
            // 스킨 추가 성공 시
            if (res) {
              console.log(res.data.data);
              this.dialog = false;
              this.getSkinList();
            }
          })
          .catch(error => {
            console.error('Error:', error);
          });

    },
    getBannerGroup(scode) {
      let mallId = 'renewwave'
      let params = {
        skinCode:scode,
        mallId:mallId
      }

      axios.get(`http://localhost:3009/api/v1/board/group`, { params })
          .then(res => {
            console.log(res.data.data)
            let result = res.data.data
            this.bannerList = result
            this.$emit(
                'update-banner-list',
                this.bannerList
            ); // 부모 컴포넌트에 전달
          })
    },
    getSkinList() {
      const mallId = 'renewwave'
      const params = {
        mallId:mallId
      }

      axios.get(`http://localhost:3009/api/v1/theme/list`, { params })
          .then(res => {
            console.log(res.data.data)
            let result = res.data.data
            this.skinList = result
          })
    },
  },
  mounted() {
    this.getSkinList()
  }
}
</script>

<style scoped>

</style>
