<template>
  <div>
    <v-dialog persistent v-model="dialog" width="auto">
      <v-card color="#fff">
        <div id="modalGuideContainer" class="modal" style="opacity: 1;">
          <!-- 가이드 (/inc/modal_guide.html) -->
          <div class="tlt_section">
            <div class="tlt">배너 매니저 이용가이드</div>
            <div class="desc">
              카페24 운영자 여러분, 안녕하세요 리뉴웨이브입니다.<br>

              리뉴웨이브 배너가이드앱은 쇼핑몰 디자인에 적용된 이미지, 텍스트 링크를
              쉽고 간편하게 교체하고 관리할 수 있는 앱입니다.

              앞으로도 더 많은 운영자분들에게 작게나마 도움이 되면 좋겠습니다.

              감사합니다.
            </div>
          </div>
          <div class="conts_section">
            <!--div class="step_1">
              <div class="h1">배너매니저 앱 설치</div>
              <div class="cont">
                <ul>
                  <li>카페24 관리자화면에서 [앱] &gt; [앱스토어 바로가기] &gt; <strong>[웹퍼블릭 배너매니저] 검색</strong></li>
                  <li>앱 상세페이지에서 <strong>[설치하기]</strong>를 클릭하여 설치를 진행합니다.</li>
                </ul>
              </div>
            </div-->

            <div class="step">
              <div class="h1">스킨 추가</div>
              <div class="cont">
                <ul>
                  <li>
                    <strong>[스킨 추가]</strong> 버튼을 클릭하여 배너를 관리할 스킨을 선택 후 <strong>[스킨 추가하기]</strong>를 클릭합니다.
                    <div class="img img1"></div>
                  </li>
                </ul>
              </div>
            </div>

            <!--div class="step">
              <div class="h1">배너 그룹 추가</div>
              <div class="cont">
                <ul>
                  <li>
                    스킨을 선택하고 <strong>[배너 그룹 추가]</strong>를 클릭하여 생성합니다.
                    <div class="img img5"></div>
                  </li-->
            <!--li>
              코드를 복사합니다.
              <div class="img img6"></div>
            </li-->
            <!--/ul>
          </div>
        </div-->

            <!--div class="step">
              <div class="h1">배너 등록</div>
              <div class="cont">
                <ul>
                  <li>배너영역을 선택한 후에는, 배너추가버튼을 클릭하여, 배너영역에 적용할 이미지를 등록합니다.<div class="img img7"></div>
                  </li-->
            <!--li>
              디자인편집창에서 <strong>[전체화면보기(skin)] &gt; [레이아웃(layout)] &gt; [기본 레이아웃(basic)]</strong> 폴더로 들어가서 레이아웃 파일을
              엽니다.
              <span class="txt">예시 이미지는 웹퍼블릭 스킨을 기준으로 하므로 타스킨에서는 파일이 상이할 수 있습니다.</span>
              <div class="img img8"></div>
            </li>
            <li>각 파일을 열고, <strong>&lt;/body&gt; 태그 위</strong>에 복사한 스크립트를 붙여넣기 합니다. </li-->
            <!--/ul>
          </div>
        </div-->

            <div class="step">
              <div class="h1">배너 그룹 추가</div>
              <div class="cont">
                <ul>

                  <li>
                    스킨 선택 후, 좌측메뉴의 [배너 그룹 추가] 버튼을 클릭하여 그룹을 생성합니다.
                    <!-- <span class="txt">배너그룹은 배너가 노출되는 영역으로, 그룹 하나당 여러개의 이미지를 등록할 수 있습니다.</span> -->
                    <div class="img img2"></div>
                  </li>
                </ul>
              </div>
            </div>

            <div class="step">
              <div class="h1">배너 이미지 추가</div>
              <div class="cont">
                <ul>
                  <li>
                    배너 그룹을 선택 후 [배너 추가] 버튼을 클릭하여 이미지, 링크, 텍스트 등을 입력합니다.
                    <div class="img img3"></div>
                  </li>
                  <li>
                    배너를 등록하고 [배너 저장]을 클릭하여 배너를 등록합니다.
                    <div class="img img4"></div>
                  </li>
                </ul>
              </div>
            </div>

            <div class="step">
              <div class="h1">배너 적용</div>
              <div class="cont">
                <ul>
                  <li>
                    배너 그룹 코드를 복사합니다.
                    <div class="img img10"></div>
                  </li>
                  <li>
                    디자인편집창에서 배너를 삽입할 파일을 열고, 복사한 코드를 붙여넣기 합니다.<span class="txt">아래 예시와 같이 배너 형태에 맞춰 제공되는 변수를 삽입하여
                            사용할 수 있습니다.</span>
                    <div class="img img11"></div>
                  </li>
                </ul>
              </div>
            </div>
          </div>
          <a
              rel="modal:close"
              class="close-modal "
              @click="$emit(
                'close-dialog',
                dialog=false);"
          >
            Close
          </a>
        </div>
      </v-card>
    </v-dialog>
  </div>
</template>

<script >
export default {
  props: {
    dialog: Boolean
  }
}
</script>

<style scoped>

</style>
