<template>
  <div class="shelf-footer" v-show="isEditMode">
    <div class="shelf-footer-tab-wrapper" v-for="item in tabs"
      :key="item.index"
      @click="onTabClick(item)">
      <div class="shelf-footer-tab" :class="{'is-selected': isSelected}">

        <!-- <div class="icon-private tab-icon" v-if="item.index === 1 && !isPrivate"></div>
        <div class="icon-private-see tab-icon" v-if="item.index === 1 && isPrivate"></div> -->

        <div class="icon-download tab-icon" v-if="item.index === 2 && !isDownload"></div>
        <div class="icon-download-remove tab-icon" v-if="item.index === 2 && isDownload"></div>

        <div class="icon-download-remove tab-icon" v-if="item.index === 3"></div>
        <div class="icon-shelf tab-icon" v-if="item.index === 4"></div>
        <div class="tab-text" :class="{'remove-text': item.index === 4}">{{label(item)}}</div>
      </div>
    </div>
  </div>
</template>

<script>
import { storeShelfMixin } from '../../utils/mixin'
import { saveBookShelf, removeLocalStorage } from '../../utils/localStorage'
import { download } from '../../api/store'
import { removeLocalForage } from '../../utils/localForage'
export default {
  mixins: [storeShelfMixin],
  computed: {
    isSelected() {
        return this.shelfSelected && this.shelfSelected.length > 0
      },
    tabs() {
      return [
        // {
        //   label: this.$t('shelf.private'),
        //   label2: this.$t('shelf.noPrivate'),
        //   index: 1
        // },
        {
          label: this.$t('shelf.move'),
          index: 3
        },
        {
          label: this.$t('shelf.download'),
          label2: this.$t('shelf.delete'),
          index: 2
        },
        {
          label: this.$t('shelf.remove'),

          index: 4
        }
      ]
    },
    isPrivate() {
      if (!this.isSelected) {
        return false
      } else {
        return this.shelfSelected.every(item => item.private)
      }
    },
    isDownload() {
      if (!this.isSelected) {
        return false
      } else {
        return this.shelfSelected.every(item => item.cache)
       }
     }
  },
  data() {
    return {
      popupMenu: null
    }
  },
  methods: {
    async downloadSelectedBook() {
      for (let i = 0; i < this.shelfSelected.length; i++) {
          await this.downloadBook(this.shelfSelected[i]).then(book => {
             book.cache = true
           })
        }
    },
    downloadBook(book) {
        let text = ''
        const toast = this.toast({
          text
        })
        toast.continueShow()
      return new Promise((resolve, reject) => {
        download(book, book => {
          // console.log('下载完毕')
          toast.remove()
          resolve(book)
        }, reject, progressEvent => {
          // console.log(progressEvent)
          // console.log(Event)
          // const current = Event.currentTarget
          // console.log(current)
          const progress = Math.floor(progressEvent.loaded / progressEvent.total * 100) + '%'
          // console.log(progress)
           text = this.$t('shelf.progressDownload').replace('$1', `${book.fileName}.epub(${progress})`)
          toast.updateText(text)
        })
      })
    },
    // 移除书籍
    removeSelectedBook() {
      Promise.all(this.shelfSelected.map(book => this.removeBook(book)))
      .then(books => {
        books.map(book => {
          book.cache = false
        })
        saveBookShelf(this.shelfList)
        this.simpleToast(this.$t('shelf.removeDownloadSuccess'))
      })
    },
    removeBook(book) {
      return new Promise((resolve, reject) => {
        removeLocalStorage(`${book.categoryText}/${book.fileName}-info`)
        removeLocalForage(`${book.fileName}`, resolve.reject)
        resolve(book)
      })
    },
    hidePopup() {
      this.popupMenu.hide()
    },
    onComplete() {
        // 隐藏popup
        this.hidePopup()
        // 取消编辑
        this.setIsEditMode(false)
        // 将所有的书籍进行一定的保存
        saveBookShelf(this.shelfList)
      },
    setPrivate() {
      let isPrivate
      if (this.isPrivate) {
        isPrivate = false
      } else {
        isPrivate = true
      }
      this.shelfSelected.forEach(book => {
        book.private = isPrivate
      })
     this.onComplete()
      if (isPrivate) {
        this.simpleToast(this.$t('shelf.setPrivateSuccess'))
      } else {
         this.simpleToast(this.$t('shelf.closePrivateSuccess'))
      }
    },

   async setDownload() {
      // let isDownload
      // if (this.isDownload) {
      //   isDownload = false
      // } else {
      //  isDownload = true
      // }
      // this.shelfSelected.forEach(book => {
      //   // 浏览器中的cache中进行显示为以缓存
      //   book.cache = isDownload
      // })

       this.onComplete()
      // 真实的下载的逻辑,下载选中的图书
      if (this.isDownload) {
        // 将下载的书籍进行移除
        this.removeSelectedBook()
      } else {
         await this.downloadSelectedBook()
         saveBookShelf(this.shelfList)
         this.simpleToast(this.$t('shelf.setDownloadSuccess'))
      }

      // if (isDownload) {
      //   this.simpleToast(this.$t('shelf.setDownloadSuccess'))
      // } else {
      //    this.simpleToast(this.$t('shelf.removeDownloadSuccess'))
      // }
      },
    // 将书籍移除书架
    removeSelected() {
      this.shelfSelected.forEach(selected => {
      this.setShelfList(this.shelfList.filter(book => book !== selected))
        })
        this.setShelfSelected([])
        this.onComplete()
    },
    // 显示是否进行私密阅读
    showPrivate() {
        this.popupMenu = this.popup({
          title: this.isPrivate ? this.$t('shelf.closePrivateTitle') : this.$t('shelf.setPrivateTitle'),
          btn: [
            {
              text: this.isPrivate ? this.$t('shelf.close') : this.$t('shelf.open'),
              click: () => {
                this.setPrivate()
              }
            },
            {
              text: this.$t('shelf.cancel'),
              click: () => {
                this.hidePopup()
              }
            }
          ]
        }).show()
      },
      // 显示是否进行下载
    showDownload() {
        this.popupMenu = this.popup({
          title: this.isDownload ? this.$t('shelf.removeDownloadTitle') : this.$t('shelf.setDownloadTitle'),
          btn: [
            {
              text: this.isDownload ? this.$t('shelf.delete') : this.$t('shelf.open'),
              click: () => {
                this.setDownload()
              }
            },
            {
              text: this.$t('shelf.cancel'),
              click: () => {
                this.hidePopup()
              }
            }
          ]
        }).show()
      },
      // 显示是否移除书架
    showRemove() {
      let title
      if (this.shelfSelected.length === 1) {
        title = this.$t('shelf.removeBookTitle').replace('$1', `《${this.shelfSelected[0].title}》`)
      } else {
        title = this.$t('shelf.removeBookTitle').replace('$1', this.$t('shelf.selectedBooks'))
      }

        this.popupMenu = this.popup({
          title: title,
          btn: [
            {
              text: this.$t('shelf.removeBook'),
              type: 'danger',
              click: () => {
                this.removeSelected()
              }
            },
            {
              text: this.$t('shelf.cancel'),
              click: () => {
                this.hidePopup()
              }
            }
          ]
        }).show()
    },
    onTabClick(item) {
      // 当没有被选中的时候则是不做处理
        if (!this.isSelected) {
          return
        }
        switch (item.index) {
          case 1:
            this.showPrivate()
            break
          case 2:
            this.showDownload()
            break
          case 3:
            this.dialog().show()
            break
          case 4:
            this.showRemove()
            break
          default:
            break
        }
   },
      label(item) {
        switch (item.index) {
          case 1:
            return this.isPrivate ? item.label2 : item.label
          case 2:
            return this.isDownload ? item.label2 : item.label
          default:
            return item.label
        }
      }
  }
}
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
  @import "../../assets/styles/global";
  .shelf-footer {
    position: fixed;
    bottom: 0;
    left: 0;
    z-index: 120;
    display: flex;
    width: 100%;
    height: px2rem(48);
    background: white;
    box-shadow: 0 px2rem(-2) px2rem(4) 0 rgba(0, 0, 0, .1);
     .shelf-footer-tab-wrapper {
      flex: 1;
      width: 25%;
      height: 100%;
      .shelf-footer-tab {
        width: 100%;
        height: 100%;
        opacity: .5;
        @include columnCenter;
        &.is-selected {
          opacity: 1;
        }
        .tab-icon {
          font-size: px2rem(20);
          color: $color-green;
        }
        .tab-text {
          margin-top: px2rem(5);
          font-size: px2rem(12);
          color:$color-green;
          // &.remove-text {
          //   color: $color-green;
          // }
        }
        // .icon-shelf {
        //   color: $color-green;
        // }
      }
    }
  }
</style>
