<template>
  <div style="background:#e7e7e7;min-height:100vh;margin:0px; padding-bottom:15vw;">
    <mt-header title="我的好友" fixed="true" style="height:10vw;background:#57D6DD;">
      <router-link to="/user" slot="left">
        <mt-button icon="back">返回</mt-button>
      </router-link>
      <router-link to="/addFriend" slot="right">
        <mt-button>
          <i class="iconfont icon-tianjiahaoyou" style="font-size:20px"></i>
        </mt-button>
      </router-link>
    </mt-header>

    <div class="module">
      <div style=" margin-top: 10vw;" class="f-title" @click="friendSlide">全部好友 ({{friendOnline}}/{{friendLength}})
        <i class="iconfont" :class="friendsShow ? iconDown : iconUp"></i>
      </div>
      <div v-for="(value,index) in this.GLOBAL.friendLists.friends" v-bind:key="index" v-show="friendsShow">
        <div class="f-list" v-if="index!='online'&&value">
          <div class="f-info" @click="goto('/chat', value, 1, index)">
            <div class="f-info">
              <div class="f-pic">
                <img v-bind:src="host + value.head">
                <mt-badge type="error" size="small" class="badge friend-badge" v-show="msgList[value.id]">{{msgList[value.id]}}</mt-badge>
              </div>
              <div class="f-main">
                <div class="f-username">{{value.nickname}}
                  <span v-if="value.on_status">[在线]</span>
                  <span v-else>[离线]</span>
                </div>
                <!-- <div v-if="msgList[value.id]" class="f-medal">
                                    {{msgList[value.id][Object.keys(msgList[value.id]).length-1].content}}
                                </div> -->
                <span class="f-medaltitle">勋章：</span>
                <div class="f-medal">
                  <div class="one-medal" v-for="(medal,mIndex) in value.medals" v-bind:key="mIndex">
                    <img v-bind:src="host + medal">
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="f-btn">
            <button class="del" @click="friendDel(value)">删除好友</button>
          </div>
        </div>
      </div>
    </div>
    <div class="module">
      <div class="f-title" @click="strangerSlide">陌生人({{strangeOnline}}/{{strangeLength}})
        <i class="iconfont" :class="strangeShow ? iconDown : iconUp"></i>
      </div>
      <div v-for="(value,index) in this.GLOBAL.friendLists.strange" v-bind:key="index" v-show="strangeShow">
        <div class="f-list" v-if="index!='online'&&value">
          <div class="f-info" @click="goto('/chat',value,2, index)">
            <div class="f-info">
              <div class="f-pic">
                <img v-bind:src="host+value.head">
                <mt-badge type="error" size="small" class="badge friend-badge" v-show="msgList[value.id]">{{msgList[value.id]}}</mt-badge>
              </div>
              <div class="f-main">
                <div class="f-username">{{value.nickname}}
                  <span v-if="value.on_status">[在线]</span>
                  <span v-else>[离线]</span>
                </div>
                <!-- <div v-if="msgList[value.id]" class="f-medal">
                                {{msgList[value.id][Object.keys(msgList[value.id]).length-1].content}}
                            </div> -->
                <span class="f-medaltitle">勋章：</span>
                <div class="f-medal">
                  <div class="one-medal" v-for="(medal,mIndex) in value.medals" v-bind:key="mIndex">
                    <img v-bind:src="host + medal">
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="f-btn">
            <button class="del" @click="delStrange(value,index)">删除</button>
          </div>
        </div>
      </div>
    </div>

    <div class="module">
      <div class="f-title" @click="systemSlide">系统消息({{requestNum+noticeNum+checkSystemNotice()}})
        <i class="iconfont" :class="systemShow ? iconDown : iconUp"></i>
        <!-- <span style="font-size:3vw;color:#666;float:right;" 
        v-show="requestNum + noticeNum">[未读消息({{requestNum+noticeNum}})]</span> -->
      </div>
      <div v-show="systemShow">
        <div class="f-list">
          <div class="f-info" @click="gotoSystemNOtice">
            <div class="f-info">
              <div class="f-pic" style="border-radius:0px;">
                <img src="static/systemNotice.png">
                <mt-badge type="error" size="small" class="badge friend-badge" v-if="checkSystemNotice()">{{checkSystemNotice()}}</mt-badge>
              </div>
              <div class="f-main">
                <div class="f-username">系统通知<span>[在线]</span></div>
                <span class="f-medaltitle">勋章：</span>
                <div class="f-medal">
                  <div class="one-medal" v-for="(medal,mIndex) in medals" v-bind:key="mIndex">
                    <img v-bind:src="medal">
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-for="(value,index) in this.GLOBAL.friendLists.system" :key="index" v-show="systemShow">
        <div class="f-list" v-if="value">
          <div class="f-info">
            <div class="f-pic">
              <img v-bind:src="host+value.head">
            </div>
            <div class="f-main">
              <div class="f-username">{{value.nickname}}
                <span v-if="value.on_status">[在线]</span>
                <span v-else>[离线]</span>
                <!-- {{showDate(value.time)}} -->
              </div>
              <div class="f-medal" v-if="value.status==5" style="width:60vw;font-size:3vw;">
                {{value.content}}
              </div>
              <div class="f-medal" v-else style="font-size:3vw;">
                {{value.content}}
              </div>
            </div>
          </div>
          <div v-if="value.status==3" class="f-btn">
            <button class="accept" @click="agreen(value,6,index)">接受</button>
            <button class="del" @click="agreen(value,4,index)">拒绝</button>
          </div>
          <div v-else-if="value.status==4" class="f-btn">
            <button class="accept">已拒绝</button>
            <button class="del" @click="delMessage(value,index)">删除</button>
          </div>
          <div v-else-if="value.status==6" class="f-btn">
            <button class="accept">已通过</button>
            <button class="del" @click="delMessage(value,index)">删除</button>
          </div>
          <div v-else class="f-btn" style="width:">
            <button class="del" @click="delMessage(value,index)">删除</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
console.log("USER_PAGEFRIEND_FRIEND_VUE");
export default {
  data() {
    return {
      friendsShow: true,
      strangeShow: false,
      systemShow: false,
      host: this.GLOBAL.Host,
      iconDown: "icon-jiantouxia",
      iconUp: "icon-jiantoushang",
      friendLength: 0,
      friendOnline: 0,
      strangeLength: 0,
      strangeOnline: 0,
      systemLength: 0,
      noticeNum: 0, //系统提醒消息数
      requestNum: 0, //好友请求消息数
      msgList: this.GLOBAL.friendMsgNum,
      medals: [
        'static/system/1.png',
        'static/system/2.png',
        'static/system/3.png',
        'static/system/4.png',
        'static/system/5.png',
      ]
    };
  },
  watch: {
    systemShow: function() {
      if (this.systemShow) {
        this.changeSystemReadStatus();
      }
    }
  },
  mounted: function() {
    console.log("friend->mounted...");
    console.log(this.GLOBAL.msgList.systemNotice);
    let that = this;
    this.checkLogin();
    this.onmessage(function() {
      console.log("firend->message->callback...");
      that.calculateNumber();
    });
    // 计算在线人数和总人数
    this.calculateNumber();
    if (this.systemShow) {
      // 修改消息的阅读状态
      this.changeSystemReadStatus();
    }
    // 显示底部导航栏
    this.$parent.tabbarShow = true;
    this.$parent.tabbarWhic = false;
    localStorage.setItem("bc_friendInfo", null);
    this.GLOBAL.friendInfo = {};
  },
  updated: function() {
    console.log("friend->updated...");
    if (this.systemShow && (this.requestNum || this.noticeNum)) {
      this.changeSystemReadStatus();
    }
  },
  methods: {
    // 检测系统通知未读消息条数
    checkSystemNotice() {
      var len = 0;
      // return len;
      if (this.GLOBAL.msgList.systemNotice) {
        for (var index in this.GLOBAL.msgList.systemNotice) {
          // console.log(this.GLOBAL.msgList.systemNotice[index].flag);
          if (!this.GLOBAL.msgList.systemNotice[index].flag) {
            len++;
          }
        }
      }

      return len;
    },
    /**
     * 跳转通知页面
     */
    gotoSystemNOtice() {
      this.$router.push({
        path: "/systemNotice",
        query: {}
      });
    },
    goto: function(pagePath, userInfo, status, index) {
      if (status == 2) {
        if (!userInfo.stmessage) {
          this.$messagebox
            .confirm("对方设置了拒收陌生人消息，是否删除？")
            .then(action => {
              this.delStrange(userInfo, index);
            })
            .catch(err => {});
          return false;
        } else if (!this.GLOBAL.userInfo.stmessage) {
          this.$messagebox
            .confirm(
              "你设置了拒收陌生人消息，对方无法向你发送消息，是否现在是修改设置？"
            )
            .then(action => {
              this.$router.push("/mySystem");
            })
            .catch(err => {});
          return false;
        }
      }
      let data = {
        info: userInfo,
        status: status
      };
      console.log("(((((((((((((((((saveFriendInfo))))))))))))))))))");
      this.setGlobalAttribute(this.GLOBAL.friendInfo, data);
      console.log(this.GLOBAL.friendInfo);
      localStorage.setItem(
        "bc_friendInfo",
        JSON.stringify(this.GLOBAL.friendInfo)
      );
      console.log(JSON.stringify(data));
      console.log(localStorage.getItem("bc_friendInfo"));
      console.log("(((((((((((((((((saveFriendInfo))))))))))))))))))");
      this.$router.push(pagePath);
    },
    /**
     * 删除系统消息
     * @param value 消息信息
     * @param index 索引值
     */
    delMessage: function(value, index) {
      console.log("<deletevalue>");
      console.log(value);
      console.log("</deletevalue>");
      let data = {
        send_id: this.GLOBAL.userInfo.id,
        msg_id: value.mid,
        type: "DeleteMessage",
        con_id: this.GLOBAL.connectionId
      };
      let that = this;
      this.senddata({
        data: data,
        callback: function(result) {
          console.log(result);
          // 删除成功，清理消息队列
          that.GLOBAL.friendLists.system[index] = null;
          // 系统总消息数减一
          that.systemLength--;
          // if (result.result) {
          //   // that.$forceUpdate();
          //   that.$messagebox.alert("删除成功！");
          // } else {
          //   that.$messagebox.alert("删除失败！");
          // }
        },
        handType: "user"
      });
    },
    /**
     * 计算在线人数和总人数或者系统消息总条数以及未读条数
     */
    calculateNumber: function() {
      console.log("friend->methods->calculateNumber...");
      // 初始化所有人数
      this.friendLength = 0; // 好友总人数
      this.friendOnline = 0; // 好友在线数
      this.strangeLength = 0; // 陌生人总人数
      this.strangeOnline = 0; // 陌生人在线数
      this.systemLength = 0; // 系统消息条数（包括好友申请和系统提醒消息）
      this.requestNum = 0; // 好友请求数
      this.noticeNum = 0; // 系统提醒消息条数
      for (var index in this.GLOBAL.friendLists) {
        for (var jndex in this.GLOBAL.friendLists[index]) {
          if (this.GLOBAL.friendLists[index][jndex]) {
            if (index == "friends") {
              if (this.GLOBAL.friendLists[index][jndex].on_status) {
                // 好友在线数
                this.friendOnline += 1;
              }
              // 好友总人数
              this.friendLength += 1;
            } else if (index == "strange") {
              if (this.GLOBAL.friendLists[index][jndex].on_status) {
                // 陌生人在线数
                this.strangeOnline += 1;
              }
              // 陌生人总人数
              this.strangeLength += 1;
            } else {
              if (!this.GLOBAL.friendLists[index][jndex].flag) {
                if (this.GLOBAL.friendLists[index][jndex] == 3) {
                  // 好友请求数
                  this.requestNum += 1;
                } else {
                  // 系统消息条数
                  this.noticeNum += 1;
                }
              }
              this.systemLength += 1;
            }
          }
        }
      }
    },
    // 修改好友的下拉状态
    friendSlide: function() {
      this.friendsShow = !this.friendsShow;
    },
    // 修改陌生人的下拉状态
    strangerSlide: function() {
      this.strangeShow = !this.strangeShow;
    },
    // 修改系统消息的下拉状态
    systemSlide: function() {
      this.systemShow = !this.systemShow;
    },
    /**
     * 删除好友
     * @param value 好友信息
     */
    friendDel: function(value) {
      this.$messagebox
        .confirm("确定要删除好友" + value.nickname + "吗？")
        .then(action => {
          let data = {
            type: "deleteFriend",
            send_id: this.GLOBAL.userInfo.id,
            nickname: this.GLOBAL.userInfo.nickname,
            friend_id: value.id,
            con_id: this.GLOBAL.connectionId,
            fcon_id: value.connectionID
          };
          // console.log("+++++++++++++delfriend++++++++++++++");
          // console.log(data);
          // console.log("+++++++++++++delfriend++++++++++++++");
          // return false;
          let that = this;
          this.senddata({
            data: data,
            callback: function(result) {
              if (result.result) {
                that.$messagebox.alert(value.nickname + "已经从好友列表中除移");
                if (that.GLOBAL.friendLists.friends[value.id].on_status) {
                  that.friendOnline--;
                }
                that.friendLength--;
                // 将用户从好友列表中除移
                that.GLOBAL.friendLists.friends[value.id] = null;
                // 未读消息总条数-当前好友的未读消息条数
                that.GLOBAL.msgLength -= that.GLOBAL.friendMsgNum[value.id];
                // 清空当前好友的未读消息条数
                that.GLOBAL.friendMsgNum[value.id] = null;
                // 清空当前好友的消息列表
                that.GLOBAL.msgList[value.id] = null;
                that.$forceUpdate();
              } else {
                that.$messagebox.alert("删除失败！");
              }
            },
            handType: "user"
          });
        });
    },
    /**
     * 好友申请处理方法
     * @param value 申请人的信息
     * @param status 处理状态，6表示通过，4表示拒绝
     * @param index 索引值
     */
    agreen: function(value, status, index) {
      let data = {
        type: "AgreeFriendRequest",
        send_id: this.GLOBAL.userInfo.id,
        nickname: this.GLOBAL.userInfo.nickname,
        username: this.GLOBAL.userInfo.username,
        head: this.GLOBAL.userInfo.head,
        friend_id: value.id,
        msg_id: value.mid,
        msg_status: status,
        con_id: this.GLOBAL.connectionId,
        fcon_id: value.connectionID
      };
      let that = this;
      this.senddata({
        data: data,
        callback: function(result) {
          console.log("<RESPONE>");
          console.log(result);
          console.log("</RESPONE>");
          if (result.result) {
            console.log(status);
            if (status == 6) {
              // 未读消息总数加1
              that.GLOBAL.msgLength++;
              // 当前好友未读消息条数加1
              if (that.GLOBAL.friendMsgNum[value.id]) {
                that.GLOBAL.friendMsgNum[value.id]++;
              } else {
                that.$set(that.GLOBAL.friendMsgNum, value.id, 1);
              }
              let newList = {};
              // 判断是否存在当前好友的未读消息
              if (that.GLOBAL.msgList[value.id]) {
                let msgList = that.GLOBAL.msgList[value.id];
                let i = 0;
                // that.GLOBAL.msgList[value.id] = {};
                for (var index in msgList) {
                  if (msgList[index]) {
                    newList[i] = msgList[index];
                    i++;
                  }
                }
                newList[i] = result.msg;
              } else {
                newList[0] = result.msg;
              }
              that.$set(that.GLOBAL.msgList, value.id, {});
              // 将消息压入消息列表
              that.setGlobalAttribute(that.GLOBAL.msgList[value.id], newList);
              // 更新好友列表
              let friendInfo = {
                id: value.id,
                head: value.head,
                on_status: value.status,
                nickname: value.nickname,
                medals: value.medals,
                username: value.username,
                connectionID: value.connectionID
              };
              that.$set(that.GLOBAL.friendLists.friends, value.id, {});
              that.setGlobalAttribute(
                that.GLOBAL.friendLists.friends[value.id],
                friendInfo
              );
              // 更新陌生人列表
              if (that.GLOBAL.friendLists.strange[value.id]) {
                if (that.GLOBAL.friendLists.strange[value.id].on_status) {
                  that.strangeOnline--;
                }
                that.strangeLength--;
                that.GLOBAL.friendLists.strange[value.id] = null;
              }
            } else {
            }
            // 修改当前好友申请的状态
            // that.GLOBAL.friendLists.system[index].status = status;
            value.status = status;
            // that.GLOBAL.msgLength--;
            that.$forceUpdate();
          } else {
            that.$messagebox.alert("网络堵塞，请稍后再试！");
          }
        },
        handType: "user"
      });
    },
    changeSystemReadStatus: function() {
      if (this.noticeNum || this.requestNum) {
        let data = {
          type: "changeSystemReadStatus",
          send_id: this.GLOBAL.userInfo.id,
          con_id: this.GLOBAL.connectionId
        };
        let that = this;
        this.senddata({
          data: data,
          callback: function(result) {
            console.log("<changeStatus>");
            console.log(result);
            console.log("</changeStatus>");
            if (result.status) {
              that.GLOBAL.msgLength =
                that.GLOBAL.msgLength - that.noticeNum - that.requestNum;
              that.noticeNum = 0;
              that.requestNum = 0;
              for (var index in that.GLOBAL.friendLists.system) {
                that.GLOBAL.friendLists.system[index].flag = 1;
              }
              that.$forceUpdate();
            } else {
            }
          },
          handType: "user"
        });
      }
    },
    delStrange: function(value, index) {
      let data = {
        send_id: this.GLOBAL.userInfo.id,
        get_id: value.id,
        type: "delStrange",
        con_id: this.GLOBAL.connectionId
      };
      let that = this;
      this.senddata({
        data: data,
        callback: function(result) {
          if (result.status) {
            // 未读消息总条数 - 当前用户未读消息条数
            that.GLOBAL.msgLength -= that.GLOBAL.friendMsgNum[value.id];
            if (that.GLOBAL.friendLists.strange[value.id].on_status) {
              that.strangeOnline--;
            }
            // 清空当前用户的信息
            that.GLOBAL.friendMsgNum[value.id] = null;
            that.GLOBAL.friendLists.strange[value.id] = null;
            that.GLOBAL.msgList[value.id] = null;
            that.strangeLength--;
            that.$forceUpdate();
          } else {
            that.$messagebox.alert("网络堵塞，请稍后再试！");
          }
        },
        handType: "user"
      });
    }
  }
};
</script>

<style scoped>
a {
  display: block;
}
.module {
  padding: 0 10px;
  margin: 10px 0;
  background-color: #fff;
}
.f-title {
  text-align: left;
  height: 12vw;
  line-height: 14vw;
  font-size: 4.5vw;
}
.f-title .iconfont {
  font-size: 4vw;
  color: #999;
}
.f-list {
  border-top: 0.5vw solid #e7e7e7;
  height: 15vw;
  /* line-height: 45px; */
  padding: 2vw 0;
  display: flex;
  justify-content: space-between;
}
.friend-badge {
  display: block;
  width: 4vw;
  height: 4vw;
  font-size: 0.75rem;
  padding: 1px 1px !important;
  line-height: normal;
  position: absolute;
  left: 7vw;
  top: -1vw;
  border-radius: 4vw;
  font-size: 3vw;
  line-height: 4vw;
  text-align: center;
}
.f-main .f-medaltitle {
  float: left;
  font-size: 3vw;
  color: #999;
}
.f-pic {
  width: 12vw;
  height: 12vw;
  border-radius: 12vw;
  overflow: hidden;
  margin-right: 1vw;
  display: flex;
  justify-items: center;
  align-items: center;
  justify-content: center;
}
.f-pic img {
  max-width: 100%;
  max-height: 100%;
}
.f-info {
  display: block;
  width: 80%;
  display: flex;
  flex-wrap: nowrap;
  position: relative;
}
.f-medal {
  display: flex;
  flex-wrap: nowrap;
  /* font-size: 12px; */
  color: #999;
  /* line-height: 20px; */
}
.f-medal .one-medal {
  width: 5vw;
  height: 5vw;
  margin-left: 1vw;
  display: flex;
  justify-content: center;
  align-items: center;
}
.f-medal .one-medal img {
  max-width: 95%;
  max-height: 95%;
}
.f-username {
  /* height: 25px; */
  line-height: 5vw;
  font-size: 4vw;
  text-align: left;
}
.f-username span {
  font-size: 2.5vw;
  color: #64d7e3;
}
.f-btn {
  line-height: 10vw;
  /* height: 45px; */
  width: 47%;
  text-align: end;
  font-size: 4vw;
}
.del {
  background-color: #e7e7e7;
  color: #999;
  font-size: 2.5vw;
  border: none;
  /* height: 30px; */
  line-height: 8vw;
  padding: 0 15px;
  outline: none;
}
.accept {
  background-color: #64d7e3;
  color: #fff;
  font-size: 12px;
  border: none;
  height: 30px;
  line-height: 30px;
  padding: 0 15px;
  border: 0px;
  outline: none;
}
.icon-tianjiahaoyou:before {
  font-size: 5vw;
}
</style>