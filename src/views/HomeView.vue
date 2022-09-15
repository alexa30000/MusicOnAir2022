<template>
  <div id="home">
    <header>
      <nav>
        <div class="logo">
          <img src="../assets/logo.png" alt="" />
          Music
          <!-- Music on Air -->
        </div>
        <subTabs :sourceData="config.homeTabsData" @change="homeTabsChange" />
        <div class="avatar">
          <img :src="userInfo.profile.avatarUrl" alt="" />
          <span>{{ userInfo.profile.nickname }}</span>
        </div>
      </nav>
    </header>
    <main>
      <!-- Template -->
      <section
        class="section-common"
        v-for="sectionItem in sectionScreen"
        :key="`section-${sectionItem.id}`"
      >
        <div class="section-common-title">
          <div>
            <p>{{ sectionItem.subTitle }}</p>
            <h2>{{ sectionItem.title }}</h2>
          </div>
          <div>
            <a href="#">查看更多</a>
          </div>
        </div>
        <ul class="section-common-list">
          <li
            v-for="(listItem, listKey) in listData[sectionItem.li.source]"
            :key="`${sectionItem.id}-${listKey}`"
            :class="`${sectionItem.li.class}`"
          >
            <figure>
              <span><img :src="listItem.picUrl" alt="" /></span>
              <p>{{ listItem.name }}</p>
              <p>
                {{ listItem.desc1 }}
                {{ listItem.desc2 ? ` ${sectionItem.li.join} ` : "" }}
                {{ listItem.desc2 || "" }}
              </p>
            </figure>
          </li>
        </ul>
      </section>
      <!-- Radio -->
      <section class="section-common">
        <div class="section-common-title sub-title">
          <div>
            <h2>電台</h2>
          </div>
          <div>
            <subTabs
              :sourceData="config.dj.djTabsData"
              @change="subTabsChange"
            />
          </div>
        </div>
        <ul class="section-common-list">
          <li v-for="rmdItem in listData.dj" :key="rmdItem.id">
            <figure v-if="config.dj.type === '推薦'">
              <span>
                <img :src="rmdItem.picUrl" alt="" />
              </span>
              <p>{{ rmdItem.name }}</p>
              <p>{{ rmdItem.lastProgramName }}</p>
            </figure>
            <figure v-else-if="config.dj.type === '熱門'">
              <span> <img :src="rmdItem.picUrl" alt="" /></span>
              <p>{{ rmdItem.name }}</p>
              <p>{{ rmdItem.copywriter }}</p>
            </figure>
            <figure v-else-if="config.dj.type === '新晉熱榜'">
              <span> <img :src="rmdItem.picUrl" alt="" /></span>
              <p>{{ rmdItem.name }}</p>
              <p>{{ rmdItem.rcmdtext }}</p>
            </figure>
            <figure v-else-if="config.dj.type === '最熱主播'">
              <span> <img :src="rmdItem.avatarUrl" alt="" /></span>
              <p>{{ rmdItem.nickName }}</p>
            </figure>
          </li>
        </ul>
      </section>
    </main>
  </div>
</template>

<script>
import subTabs from "@/components/sub-tabs";

const API_ROOT =
  process.env.NODE_ENV === "development"
    ? process.env.VUE_APP_NETEASE_API_URL_DEV
    : process.env.VUE_APP_NETEASE_API_URL;

export default {
  name: "HomeView",
  components: {
    subTabs,
  },
  data() {
    return {
      API: {
        GET_RECOMMENDS: `${API_ROOT}/personalized?limit=6`,
        GET_COMMUNITY: `${API_ROOT}/top/playlist?limit=6`,
        GET_PERSON_NEWSONG: `${API_ROOT}/personalized/newsong?limit=30`,
        GET_RECOMMENDS_MV: `${API_ROOT}/personalized/mv`,
        GET_HOT_ARTISTS: `${API_ROOT}/top/artists?offset=0&limit=6`,
        GET_USER_PLAYLIST: `${API_ROOT}/user/playlist?uid=5105803737`,
        GET_ALBUM_NEWEST: `${API_ROOT}/album/newest`,
      },
      sectionData: [
        {
          id: "section-tj",
          title: "為你推薦",
          subTitle: "挑選歌曲，開始收聽",
          moreUrl: "",
          li: {
            class: "section-common-item",
            source: "recommends",
            join: "•",
          },
        },
        {
          id: "section-rgs",
          title: "熱門歌手",
          subTitle: null,
          moreUrl: "",
          li: {
            class: "section-common-item posterRound",
            source: "hotArtists",
            join: "•",
          },
        },
        {
          id: "section-zj",
          title: "我的歌單",
          subTitle: null,
          moreUrl: "",
          li: {
            class: "section-common-item",
            source: "userPlaylist",
            join: "-",
          },
        },
        {
          id: "section-xq",
          title: "專屬推薦",
          subTitle: "為你準備的新曲",
          moreUrl: "",
          li: {
            class: "section-common-item",
            source: "recommendPerson",
            join: "•",
          },
        },
        {
          id: "section-an",
          title: "最新專輯",
          subTitle: "新專輯速遞",
          moreUrl: "",
          li: {
            class: "section-common-item",
            source: "albumNewest",
            join: "•",
          },
        },
        {
          id: "section-mv",
          title: "推薦 MV",
          subTitle: null,
          moreUrl: "",
          li: {
            class: "section-mv-item",
            source: "recommendMv",
            join: "•",
          },
        },
        {
          id: "section-sq",
          title: "社群精選",
          subTitle: null,
          moreUrl: "",
          li: {
            class: "section-common-item",
            source: "community",
            join: "•",
          },
        },
      ],
      config: {
        homeTabsData: {
          name: "home",
          checked: "首頁",
          list: [
            {
              id: "home-tabs-hm",
              title: "首頁",
            },
            {
              id: "home-tabs-ep",
              title: "探索",
            },
            {
              id: "home-tabs-ma",
              title: "媒體庫",
            },
            {
              id: "home-tabs-sh",
              title: "搜尋",
            },
          ],
        },
        dj: {
          type: "",
          api: {
            熱門: {
              url: "/dj/hot?limit=10",
              key: ["djRadios"],
            },
            推薦: {
              url: "/dj/personalize/recommend?limit=10",
              key: ["data"],
            },
            新晉熱榜: {
              url: "/dj/toplist?type=new&limit=10",
              key: ["toplist"],
            },
            最熱主播: {
              url: "/dj/toplist/popular?limit=10",
              key: ["data", "list"],
            },
          },
          djTabsData: {
            name: "dj",
            checked: "推薦",
            list: [
              {
                id: "dj-tabs-rd",
                title: "推薦",
              },
              {
                id: "dj-tabs-ht",
                title: "熱門",
              },
              {
                id: "dj-tabs-nb",
                title: "新晉熱榜",
              },
              {
                id: "dj-tabs-hd",
                title: "最熱主播",
              },
            ],
          },
        },
      },
      userInfo: {
        code: 200,
        account: {
          id: 5105803737,
        },
        profile: {
          userId: 5105803737,
          nickname: "想聽音樂",
          avatarImgId: 109951167877484270,
          avatarUrl:
            "http://p4.music.126.net/Xi8T_mriuzPzRW1cW9O1JQ==/109951167877484270.jpg",
        },
      },
      listData: {
        dj: [], // Radio List
        hotArtists: [], // Hot Artist List
        community: [], // Community Playlist
        recommends: [], // Recommend Playlist
        albumNewest: [], // Album Newest List
        userPlaylist: [], // User Playlist
        recommendPerson: [], //  Recommend New Song
        recommendMv: [], // Recommand MV
      },
    };
  },
  created() {
    this.getCommunity();
    this.getRecommend();
    this.getUserPlaylist();
    this.getNewsong();
    this.getMv();
    this.getHotArtists();
    this.getAlbumNewest();
  },
  methods: {
    // Change Sub Tabs
    subTabsChange(val) {
      // console.log(val);
      this.config.dj.type = val;
      const curApi = this.config.dj.api[val];
      fetch(`${API_ROOT}${curApi.url}`).then((res) => {
        res.json().then((resJson) => {
          let data = resJson;
          curApi.key.forEach((path) => (data = data[path]));
          this.listData.dj = data;
        });
      });
    },
    // Change Main Tabs
    homeTabsChange(val) {
      if (typeof val == "string") console.log(val);
    },
    // Fetch data to json
    fetchToJson(url) {
      return new Promise((resolve, reject) => {
        fetch(url, {
          headers: {
            "Content-Type": "application/json",
            Accept: "application/json",
          },
        }).then((res) => {
          res.json().then((resJson) => {
            // console.log(resJson);
            if (resJson.code === 200) {
              resolve(resJson);
            } else {
              reject(resJson);
            }
          });
        });
      });
    },
    // Get community playlist
    getCommunity() {
      // console.log("getCommunity");
      this.fetchToJson(this.API.GET_COMMUNITY).then((resJson) => {
        this.listData.community = resJson.playlists.map((v) => {
          v.picUrl = v.coverImgUrl;
          v.desc1 = v.trackCount + "首音樂";
          return v;
        });
      });
    },
    // Get recommend playlist
    getRecommend() {
      // console.log("getRecommend");
      this.fetchToJson(this.API.GET_RECOMMENDS).then((resJson) => {
        this.listData.recommends = resJson.result.map((v) => {
          v.desc1 = this.convertDate(v.trackNumberUpdateTime / 1000);
          v.desc2 = v.trackCount + "首音樂";
          return v;
        });
      });
    },
    // Get user playlist
    getUserPlaylist() {
      // console.log("getPlaylist");
      this.fetchToJson(this.API.GET_USER_PLAYLIST).then((resJson) => {
        this.listData.userPlaylist = resJson.playlist.map((v) => {
          //console.dir(v);
          v.desc1 = this.convertDate(v.trackUpdateTime / 1000);
          v.desc2 = this.name;
          v.picUrl = v.coverImgUrl;
          return v;
        });
      });
    },
    // Get new song
    getNewsong() {
      // console.log("getNewSong");
      this.fetchToJson(this.API.GET_PERSON_NEWSONG).then((resJson) => {
        this.listData.recommendPerson = resJson.result.map((v) => {
          v.desc1 = v.song.album.type;
          v.desc2 = this.pickUpName(v.song.artists);
          return v;
        });
      });
    },
    //  Get album newest
    getAlbumNewest() {
      // console.log("getAlbumNewest");
      this.fetchToJson(this.API.GET_ALBUM_NEWEST).then((resJson) => {
        this.listData.albumNewest = resJson.albums.map((v) => {
          v.desc1 = v.name;
          v.desc2 = this.pickUpName(v.artists);
          return v;
        });
      });
    },
    //  Get recommend MV
    getMv() {
      // console.log("getMv");
      this.fetchToJson(this.API.GET_RECOMMENDS_MV).then((resJson) => {
        this.listData.recommendMv = resJson.result.map((v) => {
          v.desc1 = v.artistName;
          v.desc2 = v.playCount + "萬次播放";
          return v;
        });
      });
    },
    // Get hot artists
    getHotArtists() {
      // console.log("getArtists");
      this.fetchToJson(this.API.GET_HOT_ARTISTS).then((resJson) => {
        this.listData.hotArtists = resJson.artists.map((v) => {
          v.picUrl = v.img1v1Url;
          v.desc1 = "歌手";
          return v;
        });
      });
    },
  },
  computed: {
    //  convert json to date
    convertDate() {
      return function (date) {
        const recordDate = new Date(date * 1000);
        const year = recordDate.getFullYear();
        const month = recordDate.getMonth() + 1;
        const day = recordDate.getDate();
        return `${year}年${month}月${day}日`;
      };
    },
    //  retrieve actrist name
    pickUpName() {
      return function (artists, Separator = "/") {
        return artists
          .map((n) => {
            if (n.name) return n.name;
            else return n;
          })
          .join(Separator);
      };
    },
    // Section screen, show only data not empty
    sectionScreen() {
      return this.sectionData.filter(
        (v) => this.listData[v.li.source].length > 0
      );
    },
  },
};
</script>

<style lang="less">
#home {
  width: 100%;
  min-height: 100vh;
  position: relative;
  background-color: rgba(0, 0, 0, 1);

  #main-bg {
    position: absolute;
    width: 100%;
    height: 100%;
    background-image: linear-gradient(
      to top,
      rgba(0, 0, 0, 1),
      rgba(0, 0, 0, 0.6)
    );
  }

  #main-img {
    position: absolute;
    z-index: -1;
    width: 100%;
    height: 100%;
    opacity: 0.6;
    object-fit: cover;
  }

  header {
    height: 52px;
    padding: 8px 16px;
    position: sticky;
    top: 0;
    z-index: 1;
    background-color: #000;

    &::after {
      content: "";
      position: absolute;
      top: 68px;
      left: 0;
      height: 1px;
      width: 100%;
      background: rgba(255, 255, 255, 0.1);
      z-index: 2;
      transform: scaleY(0.25);
    }

    nav {
      width: 100%;
      height: inherit;
      display: flex;
      align-items: center;
      justify-content: space-between;

      div {
        font-size: 24px;
        font-weight: 500;
      }

      .logo {
        width: 100px;
        display: flex;
        align-items: flex-start;
        font-weight: bold;

        img {
          height: 34px;
          width: 34px;
          margin: 0 4px 0 0;
        }
      }

      ul {
        display: flex;
        padding: 0;

        li {
          color: rgba(255, 255, 255, 0.5);
          font-size: 20px;
          font-weight: 500;
          padding: 0 22px;

          &:first-child {
            color: #fff;
          }
        }
      }

      .avatar {
        display: flex;
        align-items: center;
        width: 100px;
        justify-content: flex-end;

        img {
          height: 30px;
          width: 30px;
          border-radius: 50%;
          margin: -2px 6px 0 0;
        }

        span {
          font-size: 14px;
        }
      }

      @media screen and (max-width: 768px) {
        & {
          ul li {
            padding: 0 16px;
          }

          .avatar {
            width: auto;
          }
        }
      }

      @media screen and (max-width: 768px) {
        & {
          ul li {
            padding: 0 10px;
          }

          .avatar {
            width: auto;
          }
        }
      }
    }
  }

  .section-common {
    padding: 32px 40px 56px;

    // main title
    .section-common-title {
      padding: 0 12px;
      margin-bottom: 16px;
      line-height: 1.5;
      display: flex;
      justify-content: space-between;
      align-items: flex-end;

      p {
        font-size: 14px;
        font-weight: 400;
        color: rgba(255, 255, 255, 0.6);
      }

      h2 {
        line-height: 1.5;
        font-size: 28px;
        font-weight: 700;
      }
    }

    // sub title
    .sub-title {
      display: flex;
      align-items: flex-end;
      justify-content: flex-start;
    }

    ul[class="section-common-list"] {
      display: flex;
      overflow: hidden;
      width: 100%;

      li {
        flex-shrink: 0;
        cursor: pointer;
        position: relative;
        width: calc(100% / 6);
        padding: 0 12px;
        box-sizing: border-box;

        img {
          border-radius: 12px;
          width: 100%;
        }

        figure span::after {
          content: "";
          position: absolute;
          top: 0;
          left: 0;
          right: 0;
          bottom: 0;
          opacity: 0;
          transition: all 0.3s;
          background: linear-gradient(
            to top,
            rgba(0, 0, 0, 0),
            rgba(0, 0, 0, 0.7)
          );
        }

        &:hover figure span::after {
          opacity: 1;
        }

        p {
          font-size: 14px;
          white-space: nowrap;
          overflow: hidden;
          text-overflow: ellipsis;

          &:last-child {
            text-decoration: none;
            color: rgba(255, 255, 255, 0.7);
          }
        }

        &[class="section-mv-item"] {
          width: 25%;

          figure {
            background: rgba(24, 24, 24, 1);
            border-radius: 12px;
            padding: 16px;

            img {
              width: 100%;
              height: 190px;
              object-fit: cover;
              margin-bottom: 10px;
              box-shadow: rgba(0, 0, 0, 0.5) 0px 8px 24px 0px;
            }
          }

          @media screen and (max-width: 1068px) {
            width: 33.33%;
          }

          @media screen and (max-width: 968px) {
            width: 50%;
          }
        }

        @media screen and (max-width: 1068px) {
          width: calc(100% / 5);
          padding: 0 10px;
        }

        @media screen and (max-width: 968px) {
          width: calc(100% / 4);
          padding: 0 8px;
        }

        @media screen and (max-width: 768px) {
          width: calc(100% / 3);
          padding: 0 8px;
        }
      }

      .posterRound {
        figure {
          background: #181818;
          border-radius: 12px;
          padding: 12px 12px 24px;

          img {
            border-radius: 50%;
          }

          p {
            text-align: center;
          }
        }
      }
    }
  }
}
</style>
