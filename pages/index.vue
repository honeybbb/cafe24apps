<template>
  <div id="app" data-app="true">
    <section id="container" class="main">
      <div class="wrapper">

      <skinList
          :themes="themes"
          :mo-theme="mo_themes"
          @update-banner-list="handleBannerUpdate"
          @on-skin-name="onSkinName"
      />

      <div class="groupBox">
        <div class="groupListBox">
          <div class="groupTop">
            <h2 class="title">배너 그룹</h2>
            <a @click="addGroup" class="btnAdd">배너 그룹 추가</a>
          </div>
          <ul class="groupList">
            <li
                v-for="banner in bannerList"
                class="groupItem"
                :data-banner-code="banner.b_code"
                @click="getBannerContent(banner.title, banner.b_code)"
                :class="bcode==banner.b_code?'active':''"
            >
              <div class="groupItemInfo">
                <div class="groupName">
                  {{ banner.title }}
                  <span class="no">(1)</span>
                </div>
                <div class="is_active">
                  <input
                      type="checkbox"
                      name="is_active"
                      v-model="banner.displayYn"
                      :true-value="'Y'"
                      :false-value="'N'"
                      @change="toggleDisplayYn(banner.b_code, banner.displayYn)"
                  />
                </div>
              </div>
              <div class="groupItemBtn">
                <a @click="dialog4=true" class="btnCode ">배너코드</a>
                <a @click="ModifyGroup(banner)" class="btnEdit">수정</a>
                <a @click="DeleteGroup(banner.b_code)" class="btnDel">삭제</a>
              </div>
            </li>
          </ul>
        </div>
        <div class="groupDataBox">
          <div class="groupDataTop">
            <h4 class="title">{{ groupName }}</h4>
            <div class="btnBox">
              <a class="btnProcess" @click="sendHtmlData">데이터 전송</a>
              <a class="btnAdd" @click="addBannerContent">배너 추가</a>
              <a @click="setBannerContent" class="btnSave">배너 저장</a>
            </div>
          </div>
          <div class="groupDataList">
            <ul class="groupDataInfo">
              <li class="g1">순서</li>
              <li class="g2">이미지</li>
              <li class="g3">정보</li>
              <li class="g4">사용여부</li>
            </ul>
          </div>
          <div class="groupDataItemBox">
            <ul
                class="groupDataItem"
                v-for="(content, index) in contentList"
                :key="index"
                draggable="true"
                @dragstart="dragStart(index)"
                @dragover.prevent
                @drop="drop(index)"
            >
              <li class="g1">{{ index + 1 }}</li>
              <li class="g2">
                <div class="groupDataImage">
                  <div class="thumb_pc thumbArea" @click="fileSelect1(index)">
                    <div class="btnRemove"><a @click="deleteSelect1(content)" class="delThumb">삭제</a></div>
                    <input
                        type="file"
                        class="attach-file displaynone"
                        accept="image/*"
                        :ref="'pc_img_'+index"
                        @change="previewImage1(index)"
                    >
                    <div class="thumbnail">
                      <v-img
                        height="100%"
                        :src="getPcImageSrc(index, content.b_code, content.file_edit1)" />
<!--                        :src="`https://renewwave.cafe24.com/renewImg/image/pc/${content.b_code}/${content.file_edit1}`"/>-->
<!--                     <v-img :src="content.file_edit1? `'${content.file_edit1}'`:'/assets/empty.jpg'" id="preview_thumbnail_pc" @error="setDefaultImage($event, 'pc')" />-->
                      <!--v-img v-else :src="require('assets/empty.jpg')"/-->
                    </div>
                    <div class="thumb_txt">PC이미지</div>
                  </div>
                  <div class="thumb_mob thumbArea" @click="fileSelect2(index)">
                    <div class="btnRemove"><a @change="deleteSelect2(content)" class="delThumb">삭제</a></div>
                    <input
                        type="file"
                        class="attach-file displaynone"
                        accept="image/*"
                        :ref="'mo_img_'+index"
                        @change="previewImage2(index)"
                    >
                    <div class="thumbnail">
                      <!--v-img :src="content.file_edit2"/-->
                      <v-img
                          height="100%"
                          :src="getMoImageSrc(index, content.b_code, content.file_edit2)"/>
<!--                          :src="`https://renewwave.cafe24.com/renewImg/image/m/${content.b_code}/${content.file_edit2}`"/>-->
                      <!--v-img v-else :src="require('assets/empty.jpg')"/-->
                    </div>
                    <div class="thumb_txt">모바일이미지</div>
                  </div>
                </div>
              </li>
              <li class="g3">
                <div class="textArea">
                  <div class="sec inputBox">
                    <div class="title">타이틀<!--span class="count">1</span--></div>
                    <div class="data">
                      <input type="text" name="txt" placeholder="제목을 입력해 주세요." autocomplete="off" v-model="content.title">
                    </div>
                    <!--div class="btn"><a @click="setBoardContent" class="btnAdd">추가</a></div-->
                  </div>
                  <div class="sec inputBox">
                    <div class="title">설명<!--span class="count">2</span--></div>
                    <div class="data">
                      <input type="text" name="txt" placeholder="내용을 입력해 주세요." autocomplete="off" v-model="content.description">
                    </div>
                    <!--div class="btn">
                      <a href="javascript:;" class="btnDel">삭제</a>
                    </div-->
                  </div>
                </div>
                <div class="sec inputBox">
                  <div class="title">링크</div>
                  <div class="data">
                    <input type="text" name="link" placeholder="클릭시 이동될 URL을 입력하세요." autocomplete="off" v-model="content.link">
                  </div>
                  <!--div class="btn">
                    <select name="pageOpen" class="select">
                      <option value="_self">현재창</option>
                      <option value="_parent">부모창</option>
                      <option value="_blank">새창</option>
                    </select>
                  </div-->
                </div>
                <div class="sec dateBox">
                  <div class="title">예약노출<span class="state"></span></div>
                  <div class="data">
                    <v-radio-group row v-model="content.checkShow" color="primary" hide-details>
                      <v-radio value="N" label="항상 노출함"></v-radio>
                      <v-radio value="Y" label="노출 기간 설정"></v-radio>
                    </v-radio-group>
                    <!--label><input type="radio" name="is_period" value="F">항상	노출함</label>
                    <label><input type="radio" name="is_period" value="T">노출 기간 설정</label-->
                    <div class="calendar " v-show="content.checkShow=='Y'">
                      <date-picker v-model="content.showStartDt" type="datetime" :lang="lang" class="startDate" placeholder="0000-00-00 00:00:00" />
                      ~
                      <date-picker v-model="content.showEndDt" type="datetime" :lang="lang" class="endDate" placeholder="0000-00-00 00:00:00"/>
                    </div>
                  </div>
                </div>
              </li>
              <li class="g4">
                <div class="btnBox">
                  <div class="checkbox">
                    <input
                        type="checkbox"
                        class="toggleInput"
                        name="is_active"
                        v-model="content.displayYn"
                        :true-value="'Y'"
                        :false-value="'N'"
                    >
                    <label class="toggleInputTxt"></label>
                  </div>
                  <a class="btnCopy" @click="CopyBannerContent(content)">복사</a>
                  <a class="btnDel" @click="DeleteContent(content.seq)">삭제</a>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
    </section>

    <v-dialog v-model="dialog" width="auto">
      <v-card color="#fff">
        <div id="modalGroupContainer" style="opacity: 1; display: inline-block;">
        <form id="modalGroupForm">
          <div class="modal__add_group">
            <div class="tlt_section">
              <div class="tlt">{{ isMode == 'add' ? '배너 그룹 추가' : '배너 그룹 수정' }}</div>
            </div>
            <div class="info_section">
              <ul>
                <li class="g_name">
                  <div class="tlt">배너그룹명 <span class="required">*</span></div>
                  <div class="sec">
                    <input
                        type="text"
                        name="name"
                        placeholder="배너그룹명을 입력하세요."
                        autocomplete="off"
                        v-model="g_name"
                    >
                  </div>
                </li>
                <li class="g_desc">
                  <div class="tlt">설명</div>
                  <div class="sec">
                    <textarea
                        name="desc"
                        placeholder="배너그룹 설명글을 입력하세요."
                        style="width:100%;height:100px;"
                        v-model="g_desc"
                    ></textarea>
                    <!--<input type="text" name="desc" placeholder="배너그룹 설명글을 입력하세요."></div>-->
                  </div>
                </li>
                <li class="g_type">
                  <div class="tlt">사용 여부</div>
                  <div class="sec">
                    <v-radio-group row v-model="g_type" hide-details>
                      <v-radio value="Y" label="사용함"></v-radio>
                      <v-radio value="N" label="사용안함"></v-radio>
                    </v-radio-group>
                  </div>
                </li>
              </ul>
            </div>
            <div class="btn_section">
              <a
                @click="setBannerGroup"
                class="btn-basic btn-big btn-add-group"
              >
                {{ isMode == 'add'? '추가하기':'수정하기' }}
              </a>
            </div>
          </div>
        </form>
      </div>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialog2" width="800">
      <v-card color="#fff">
        <div
          id="bannerFormContainer"
          class="modal"
          data-mode="add"
          style="opacity: 1; "
        >
      <div class="banner-area">
        <div class="banner-form">
          <form class="form">
            <div class="add_section">
              <div class="list_section">
                <ul>
                  <li class="g1"><span class="move_handle btn_move"></span></li>
                  <li class="g2">
                    <div class="thumb">
                      <div class="thumb_pc thumb-area" @click="fileSelect1">
                        <!--div class="btn remove">
                          <a href="javascript:;" class="btn-small del-thumb">삭제</a>
                        </div-->
                        <input
                          type="file"
                          class="attach-file displaynone"
                          accept="image/*"
                          autocomplete="off"
                          ref="pc_img"
                          @change="previewImage1"
                        >
                        <div class="thumbnail">
                          <v-img :src="b_file_edit1"/>
                        </div>
                        <div class="thumb_txt">PC이미지</div>
                      </div>

                      <div class="thumb_mob thumb-area" @click="fileSelect2">
                        <!--div class="btn remove">
                          <a href="javascript:;" class="btn-small del-thumb">삭제</a>
                        </div-->
                        <input
                            type="file"
                            class="attach-file displaynone"
                            accept="image/*"
                            autocomplete="off"
                            ref="mo_img"
                            @change="previewImage2"
                        >
                        <div class="thumbnail">
                          <v-img :src="b_file_edit2"/>
                        </div>
                        <div class="thumb_txt">모바일이미지</div>
                      </div>

                    </div>
                  </li>
                  <li class="g3">
                    <div class="text-area">
                      <div class="sec input-box">
                        <div class="h1">타이틀<!--span class="count">1</span--></div>
                        <div class="h2">
                          <input
                              type="text"
                              name="txt"
                              placeholder="내용을 입력해 주세요."
                              autocomplete="off"
                              v-model="b_name"
                          >
                        </div>
                        <!--div class="h3"><a href="javascript:;" class="btn btn-add btn-bm btn-small" data-action="add-text">추가</a></div-->
                      </div>
                    </div>
                    <div class="sec inputBox">
                        <div class="h1">설명<!--span class="count">2</span--></div>
                        <div class="h2">
                            <input
                                type="text"
                                name="txt"
                                placeholder="내용을 입력해 주세요."
                                autocomplete="off"
                                v-model="b_desc"
                            >
                        </div>
                        <!--div class="btn">
                          <a href="javascript:;" class="btnDel">삭제</a>
                        </div-->
                    </div>
                    <div class="sec">
                      <div class="h1">링크</div>
                      <div class="h2">
                        <input
                            type="text"
                            name="link"
                            placeholder="클릭시 이동될 URL을 입력하세요."
                            autocomplete="off"
                            v-model="b_link"
                        >
                      </div>
                      <!--div class="h3">
                        <select name="link_target">
                          <option value="_self">현재창</option>
                          <option value="_parent">부모창</option>
                          <option value="_blank">새창</option>
                        </select>
                      </div-->
                    </div>
                    <div class="sec">
                      <div class="h1">예약노출<span class="state"></span></div>
                      <div class="h2">
                        <v-radio-group v-model="checkShow" hide-details>
                            <v-radio value="N" label="항상 노출함"></v-radio>
                            <v-radio value="Y" label="노출 기간 설정"></v-radio>
                        </v-radio-group>
                        <div class="calendar " v-show="checkShow=='Y'">
                            <date-picker v-model="showStartDt" type="datetime" :lang="lang" class="startDate" placeholder="0000-00-00 00:00:00" />
                            ~
                            <date-picker v-model="showEndDt" type="datetime" :lang="lang" class="endDate" placeholder="0000-00-00 00:00:00"/>
                        </div>
                      </div>
                    </div>
                  </li>
                  <li class="g4">
              <span class="btn">
                <div class="checkbox-wrapper">
                  <input type="checkbox" class="tgl tgl-ios" value="Y" name="is_active" autocomplete="off">
                  <label class="tgl-btn" data-action="active_toggle"></label>
                </div>
                <a href="javascript:;" class="btn-copy btn-medium btn-bm" data-action="copy-banner">복사</a>
                <a href="javascript:;" class="btn-del btn-medium btn-bm" data-action="del-banner">삭제</a>
              </span>
                  </li>
                </ul>
              </div>
            </div>
          </form>
        </div>
        <div class="button-area">
          <a
            data-action="add-banner"
            class="btn-basic btn-big"
            @click="setBoardContent"
          >
            등록하기
          </a>
          <a
            class="btn-em btn-big"
            rel="modal:close"
            @click="dialog2=false"
          >
            닫기
          </a>
        </div>
      </div>
      </div>
      </v-card>
  </v-dialog>

    <v-dialog v-model="dialog3" width="400">
      <v-card color="#fff">
        <v-col cols="12">
          <h2>FTP 정보 초기 설정</h2>
          <v-row no-gutters>
            <div>ftp 아이디</div>
            <div>
              <input
                  type="text"
                  v-model="ftpUser"
                  style="border: 1px solid #dddddd; padding: 2px;"
              />
            </div>
          </v-row>
          <v-row no-gutters>
            <div>ftp 비밀번호</div>
            <div>
              <input
                  type="password"
                  v-model="ftpPass"
                  style="border: 1px solid #dddddd; padding: 2px;"
              />
            </div>
          </v-row>
          <div>
            <v-btn depressed color="#546ef4" @click="setFtpInfo">
              <span style="color: #fff">저장</span>
            </v-btn>
          </div>
        </v-col>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialog4" width="400">
      <v-card color="#fff">
        <div style="padding: 24px;">
          배너코드 : {{ bcode }}
        </div>

      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import axios from "axios";
import DatePicker from 'vue2-datepicker';
import 'vue2-datepicker/index.css';
import skinList from "/components/skinList";
export default {
  components: {
    DatePicker,
    skinList
  },
  data () {
    return {
      ftpUser: '',
      ftpPass: '',
      isMode: 'add',
      dialog: false, //배너그룹추가
      dialog2: false, //배너컨텐츠추가
      dialog3: false, //ftp 정보 저장
      dialog4: false, //배너코드 보기
      is_period: 'F',
      checkShow: 'N',
      showStartDt: '0000-00-00 00:00:00',
      showEndDt: '0000-00-00 00:00:00',
      calendar: false,
      scode: '',//스킨코드
      bcode: '',//배너코드
      seq: '',
      groupName: '',
      lang: {
        days: ["일","월","화","수","목","금","토"],
        months: [
          "1월",
          "2월",
          "3월",
          "4월",
          "5월",
          "6월",
          "7월",
          "8월",
          "9월",
          "10월",
          "11월",
          "12월"
        ],
        yearFormat: "YYYY년",
        monthFormat: "MM월",
        monthBeforeYear: false,
      },
      themes: [],
      mo_themes: [],
      skinList: [],
      bannerList: [
        /*
        {
          "seq":"1",
          "title":"메인 배너",
          "banner_code":"4DHbJy",
          "description":"메인 배너 입니다",
          "sort":0,
          "displayYn":"Y"
        },
        {
          "seq":"2",
          "title":"최상단 띠배너",
          "banner_code":"5j8f",
          "description":"최상단 띠 배너 입니다",
          "sort":0,
          "displayYn":"Y"
        },
        {
          "seq":"3",
          "title":"팝업",
          "banner_code":"62ec",
          "description":"팝업입니다",
          "sort":0,
          "displayYn":"Y"
        }

         */
      ],
      contentList: [],
      g_name: '', //배너그룹명
      g_desc: '', //배너그룹설명
      g_use: '', //노출순서
      g_type: 'Y', //사용여부
      b_name: '',//배너등록하기 배너타이틀
      b_desc: '',//배너등록하기 배너설명
      b_link: '',//배너등록하기 배너링크
      b_use: '', //배너등록하기 배너 예약노출
      b_startDt: '',
      b_endDt: '',
      b_file_edit1: '',
      b_file_edit2: '',
      draggedItemIndex: null, // Index of the item being dragged
    }
  },
  methods: {
    getFtpInfo() {
      // ftp 정보 가져와서 없으면
      //result.length == 0
      // this.dialog3 = true

    },
    setFtpInfo() {
      const ftpUser = this.ftpUser
      const ftpPass = this.ftpPass
      const id = "renewwave1"
      let params = new URLSearchParams();
      params.append('ftpUser', ftpUser)
      params.append('ftpPass', ftpPass)
      axios.post(`http://localhost:3009/api/v1/ftp/info/${id}`, params)
          .then(res => {
            console.log(res.data.data)
            this.dialog3 = false
          })
    },
    setDefaultImage(event, type) {
      console.log(event)
      //if(event == undefined) event.target.src = '/assets/empty.jpg';
    },
    dragStart(index) {
      this.draggedItemIndex = index;
    },
    drop(dropIndex) {
      if (this.draggedItemIndex === null) return;

      // Swap the dragged item with the target item
      const draggedItem = this.contentList[this.draggedItemIndex];
      this.contentList.splice(this.draggedItemIndex, 1);
      this.contentList.splice(dropIndex, 0, draggedItem);

      // Reset the dragged item index
      this.draggedItemIndex = null;
    },
    handleBannerUpdate(newBannerList) {
      //console.log(newBannerList, 'newBannerList')
      this.bannerList = newBannerList
    },
    onSkinName(scode) {
      this.scode = scode
    },
    fileSelect1(index) {
      // pc이미지 저장
      if(index >= 0) {
        this.$refs['pc_img_' + index][0].click();
      } else {
        this.$refs['pc_img'].click();
      }
    },
    fileSelect2(index) {
      // mo이미지 저장
      if(index >= 0) {
        this.$refs['mo_img_' + index][0].click();
      }else {
        this.$refs['mo_img'].click();
      }

    },
    getPcImageSrc(index, b_code, file_edit1) {
      console.log(this.$refs['pc_img_'+index], 'this.pc_img')
      if(this.$refs['pc_img_'+index]) {
        return file_edit1
      } else {
        return `https://renewwave.cafe24.com/renewImg/image/pc/${b_code}/${file_edit1}`
      }
    },
    getMoImageSrc(index, b_code, file_edit2) {
        if(this.$refs['mo_img_'+index]) {
          return file_edit2
      } else {
          return `https://renewwave.cafe24.com/renewImg/image/m/${b_code}/${file_edit2}`
      }
    },
    previewImage1(index) {
      if(index >= 0) {
        const fileInput = this.$refs['pc_img_' + index][0];
        const file = fileInput.files[0];
        console.log(file, 'file', index)

        if (file) {
          const reader = new FileReader();
          reader.onload = (e) => {
            console.log(e, this.contentList[index])
            // 미리보기 이미지를 items 배열에 저장
            this.contentList[index].file_edit1 = e.target.result;
          };
          reader.readAsDataURL(file);
        }
      } else {
        const fileInput = this.$refs['pc_img'];
        const file = fileInput.files[0];
        console.log(file, 'file')

        if (file) {
          const reader = new FileReader();
          reader.onload = (e) => {
            console.log(e, this.contentList[index])
            // 미리보기 이미지를 items 배열에 저장
            this.b_file_edit1 = e.target.result;
          };
          reader.readAsDataURL(file);
        }
      }

    },
    previewImage2(index) {
      if(index >= 0) {
        const fileInput = this.$refs['mo_img_' + index][0];
        const file = fileInput.files[0];
        console.log(file, 'file')

        if (file) {
          const reader = new FileReader();
          reader.onload = (e) => {
            console.log(e, this.contentList[index])
            // 미리보기 이미지를 items 배열에 저장
            this.contentList[index].file_edit2 = e.target.result;
          };
          reader.readAsDataURL(file);
        }
      } else {
        const fileInput = this.$refs['mo_img'];
        const file = fileInput.files[0];
        console.log(file, 'file')

        if (file) {
          const reader = new FileReader();
          reader.onload = (e) => {
            //console.log(e, this.contentList[index])
            // 미리보기 이미지를 items 배열에 저장
            this.b_file_edit2 = e.target.result;
          };
          reader.readAsDataURL(file);
        }
      }

    },
    deleteSelect1(content) {
        content.file_edit1 = '';
    },
    deleteSelect2(content) {
        content.file_edit2 = '';
    },
    toggleDisplayYn(bcode, displayYn) {
      const id = "renewwave";
      // const id = this.$route.params.mallId;
      // console.log(bcode, displayYn)

      let params = new URLSearchParams();
      params.append('bcode', bcode)
      params.append('displayYn', displayYn)
      axios.post(`http://localhost:3009/api/v1/board/group/display/${id}`, params)
          .then(res => {
            console.log(res => {
              console.log(res.data.data)
            })
          })
    },
    CopyBannerContent(content) {
      const newContent = JSON.parse(JSON.stringify(content));
      delete newContent.seq;
      console.log(content, 'c')
      this.contentList.push(newContent)
    },
    sendHtmlData() {
      const mallId = "renewwave";
      const bcode = this.bcode;
      const scode = this.scode;

      let filteredData = this.contentList.filter(item => item.displayYn == 'Y');

      let pc_html = "";
      let mo_html = "";

      filteredData.forEach(val => {
        if (this.scode.indexOf('mobile') == -1) {
          pc_html += `<div class="swiper-slide" data-seq="${val.seq}">`;
          pc_html += `<a href="${val.link}">`;
          pc_html += `<img src="https://${mallId}.cafe24.com/renewImg/image/pc/${val.b_code}/${val.file_edit1}" alt="${val.title}"/>`;
          pc_html += `<p class="text" data-swiper-parallax="-100">`;
          pc_html += `<span>${val.description || ''}</span>`; // null을 공백으로 대체
          pc_html += `</p>`;
          pc_html += `</a>`;
          pc_html += `</div>`;
        } else {
          mo_html += `<div class="swiper-slide" data-seq="${val.seq}">`; // 수정: data-seq의 따옴표를 닫는 부분 수정
          mo_html += `<a href="${val.link}">`;
          mo_html += `<img src="https://${mallId}.cafe24.com/renewImg/image/m/${val.b_code}/${val.file_edit2}" alt="${val.title}"/>`;
          mo_html += `<p class="text" data-swiper-parallax="-100">`;
          mo_html += `<span>${val.description || ''}</span>`; // null을 공백으로 대체
          mo_html += `</p>`;
          mo_html += `</a>`;
          mo_html += `</div>`;
        }
      });

      // pc_html과 mo_html을 그대로 전송
      axios.post(`http://localhost:3009/api/v1/upload/group/process`,
          {
                  mallId: mallId,
                  bcode: bcode,
                  scode: scode,
                  pc_html: pc_html,
                  mo_html: mo_html
                }
      )
          .then(res => {
            console.log(res.data.data);
            alert("성공적으로 전송하였습니다.")
            this.getBannerContent(this.groupName, this.bcode)
          });
    },
    formatDate(date) {
      const year = date.getFullYear();
      const month = String(date.getMonth() + 1).padStart(2, '0'); // 월은 0부터 시작하므로 +1
      const day = String(date.getDate()).padStart(2, '0');
      const hours = String(date.getHours()).padStart(2, '0');
      const minutes = String(date.getMinutes()).padStart(2, '0');
      const seconds = String(date.getSeconds()).padStart(2, '0');

      return `${year}${month}${day}${hours}${minutes}${seconds}`;
    },
    addGroup() {
      this.isMode = 'add'
      this.g_name = ''
      this.g_desc = ''
      this.g_type = ''
      this.dialog=true
    },
    ModifyGroup(banner) {
      console.log(banner)
      this.isMode = 'modify'
      this.seq = banner.seq
      this.bcode = banner.b_code
      this.g_name = banner.title
      this.g_desc = banner.description
      this.g_type = banner.displayYn
      this.dialog = true
    },
    DeleteGroup(bcode) {
      let id = 'renewwave'
      // let id = this.$route.params.mallId
      if(bcode == undefined) bcode = this.bcode;
      let result = confirm("배너를 삭제하시겠습니까?")
      //console.log(bcode, 'bcode')
      let params = {
        'bcode': bcode
      }
      if(result) {
        axios.delete(`http://localhost:3009/api/v1/board/group/delete/${id}`, { params })
            .then(res => {
              console.log(res.data.data)

            })
      }
    },
    DeleteContent(seq) {
      let id = 'renewwave'
      // let id = this.$route.params.mallId
      let bcode = this.bcode;
      let result = confirm("배너를 삭제하시겠습니까?")
      let params = {
        'bcode': bcode,
        'seq': seq
      }

      if(result) {
        axios.delete(`http://localhost:3009/api/v1/board/content/delete`, { params })
          .then(res => {
            console.log(res.data.data)
            this.getBannerContent(null, bcode)
          })
      }
    },
    setBoardContent() {
      if(!this.b_name) {
        alert("타이틀을 입력해주세요.")
        return;
      }

      this.contentList.push({
        title: this.b_name,
        description: this.b_desc,
        link: this.b_link,
        file_edit1: this.b_file_edit1,
        file_edit2: this.b_file_edit2,
        displayYn: 'Y',
        checkShow: this.b_use,
        showStartDt: this.showStartDt,
        showEndDt: this.showEndDt,
      })

      this.dialog2 = false
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
    getCafe24Design() {
      const mallId = 'renewwave'
      const params = {
        mallId:mallId
      }
      axios.get(`http://localhost:3009/api/v1/theme/pc`, { params })
        .then(res => {
          console.log(res.data.data, 'pc')
          let result = res.data.data
          this.themes = result
        })

      axios.get(`http://localhost:3009/api/v1/theme/mobile`, { params })
        .then(res => {
          console.log(res.data.data, 'mo')
          let result = res.data.data
          this.mo_themes = result
        })
    },
    setBannerGroup() {
      let mallId = "renewwave"
      // let mallId = this.$route.params.mallId
      let params = new URLSearchParams();
      params.append('s_code', this.scode)
      params.append('title', this.g_name)
      params.append('description', this.g_desc)
      //params.append('g_sort', this.g_sort)
      params.append('g_sort', 0) // 임시
      params.append('displayYn', this.g_type)
      params.append('mallId', mallId)
      if(this.isMode == 'add') {
        console.log(this.scode)
        axios.post(`http://localhost:3009/api/v1/board/group/add`, params)
            .then(res => {
              console.log(res.data.data)
              this.dialog = false;
              this.getBannerGroup()
            })
      } else {
        params.append('bcode', this.bcode)
        params.append('seq', this.seq)
        axios.post(`http://localhost:3009/api/v1/board/group/update/${mallId}`, params)
            .then(res => {
              console.log(res.data.data)
              this.dialog = false;
              this.getBannerGroup()
            })
      }
    },
    getBannerGroup() {
      let params = {
        'skinCode': this.scode,
        'mallId': "renewwave"
      }
      axios.get(`http://localhost:3009/api/v1/board/group`, { params })
          .then(res => {
            this.bannerList = res.data.data
            //console.log(this.bannerList, 'bannerList')
          })
    },
    getBannerContent(title, bcode) {
      axios.get(`http://localhost:3009/api/v1/board/content/`+bcode)
          .then(res => {
            console.log(res.data.data, 'getBannerContent')
            let result = res.data.data
            this.contentList = result
            this.bcode = bcode
            this.groupName = title
          })
    },
    addBannerContent() {
      /*
      this.b_file_edit1 = '';
      this.b_file_edit2 = '';
      this.b_name = '';
      this.b_desc = '';
      this.b_link = '';

      this.dialog2=true

       */

      this.contentList.push({
        'b_code' : this.bcode,
        'title': '',
        'description': '',
        'link': '',
        'file_edit1': '',
        'file_edit2': '',
        'sub_sort': 0,
        'displayYn': 'Y',
        'showStartDt': '',
        'showEndDt': '',
        'checkShow': 'N'
      })
    },
    setBannerContent() {
      // sort 값 지정 후 저장
      this.contentList.forEach((a, index) => {
          a.sub_sort = index
          if(a.displayYn) {
            a.displayYn = 'Y'
          }else{
            a.displayYn = 'N'
          }
      })

      console.log(this.contentList, 'contentList')
      let contents = this.contentList;
      let cLength = this.contentList.length;
      const bcode = this.bcode; // bcode를 추가하여 서버에 보냄

      for(let i=0; i<cLength; i++) {
        let params = new URLSearchParams();
        params.append('bcode', bcode);
        params.append('title', contents[i].title);
        params.append('description', contents[i].description);
        params.append('link', contents[i].link);
        //params.append('file_edit1', contents[i].file_edit1);
        //params.append('file_edit2', contents[i].file_edit2);
        params.append('subSort', contents[i].sub_sort);
        params.append('displayYn', contents[i].displayYn);
        params.append('showStartDt', contents[i].showStartDt);
        params.append('showEndDt', contents[i].showEndDt);
        params.append('checkShow', contents[i].checkShow);

        if(contents[i].seq > 0) {
          const seq = contents[i].seq;
          // 데이터를 전송해서 업데이트
          axios.post(`http://localhost:3009/api/v1/board/content/update/${seq}`, params)
              .then(res => {
                console.log(res.data.data)
                // 데이터 저장 후 이미지 저장 -> db 업데이트
                this.fileUpload(seq, i)
              })
        } else {
          // 데이터를 먼저 전송해서 저장
          axios.post(`http://localhost:3009/api/v1/board/content/add`, params)
              .then(res => {
                console.log(res.data.data)
                let seq = res.data.data.insertId
                // 데이터 저장 후 이미지 저장 -> db 업데이트
                this.fileUpload(seq, i)
              })
        }

      }

      alert("성공적으로 저장되었습니다.")
      this.getBannerContent(this.groupName, this.bcode)
    },
    fileUpload(seq, idx) {
      let mallId = "renewwave";
      let file1 = this.$refs[`pc_img_${idx}`][0].files[0]; // 파일 입력 엘리먼트에서 첫 번째 파일 가져오기
      let file2 = this.$refs[`mo_img_${idx}`][0].files[0]; // 파일 입력 엘리먼트에서 첫 번째 파일 가져오기

      console.log('file1:', file1, 'file2:', file2)

      if(file1 || file2) {
        let formData = new FormData();
        formData.append('file1', file1);
        formData.append('file2', file2);
        formData.append('bcode', this.bcode)
        formData.append('mallId', mallId)
        axios.post(`http://localhost:3009/api/v1/upload/${seq}`, formData, {
          headers: {
            'Content-Type' : 'multipart/form-data'
          }
        }).then(res => {
          console.log(res, 'file uploads')

        })
      }
    },
  },
  mounted() {
    this.getCafe24Design();

    // let divElement = document.createElement('div');
    // divElement.setAttribute('webpb-banner-code', '8oc3');
    // divElement.setAttribute('hidden', true);
  }
}

</script>
<style>
.v-input--selection-controls {
  margin-top: 0;
}
</style>
