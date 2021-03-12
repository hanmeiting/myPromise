export default class MyPromise {
  constructor(fn) {
    this['[[PromiseState]]'] = 'pending'
    this['[[PromiseResult]]'] = undefined

    this.resolveQueue = [] // 存储then方法中的成功的回调函数
    this.rejectQueue = [] // 存储then方法中的失败的回调函数

    fn(this._resolve.bind(this), this._reject.bind(this))
  }
  _resolve(successRes) {
    this['[[PromiseState]]'] = 'fulfilled'
    this['[[PromiseResult]]'] = successRes

    // 这段代码会造成死循环,所以重写了
    // let run = () => {
    //   this.resolveQueue.forEach(fn => {
    //     fn && fn(successRes)
    //   })
    // }

    let run = () => {
      let cb
      while (cb = this.resolveQueue.shift()) {
        cb && cb(successRes)
      }
    }
    let observe = new MutationObserver(run)
    observe.observe(document.body, {
      attributes: true
    })
    document.body.setAttribute('id', 'box')
  }
  _reject(errRes) {
    this['[[PromiseState]]'] = 'rejected'
    this['[[PromiseResult]]'] = errRes
    let run = () => {
      let cb
      while (cb = this.rejectQueue.shift()) {
        cb && cb(errRes)
      }
    }
    let observe = new MutationObserver(run)
    observe.observe(document.body, {
      attributes: true
    })
    document.body.setAttribute('id', 'box')

  }
  static resolve(val) {
    return new MyPromise((resolve, reject) => {
      resolve(val)
    })
  }
  static reject(val) {
    return new MyPromise((resolve, reject) => {
      reject(val)
    })
  }
  static race(arr) {
    return new MyPromise((resolve, reject) => {
      arr.forEach(item => {
        item.then(res => {
          resolve(res)
        }, err => {
          reject(err);
        })
      })
    })
  }
  static all(arr) {
    return new MyPromise((resolve, reject) => {
      let lists = []
      let length = 0
      arr.forEach(item => {
        item.then(res => {
          length++
          lists.push(res)
          if (length >= arr.length) {
            resolve(lists)
          }
        }, err => {
          reject(err);
        })
      })
    })
  }
  finally(fn) {
    this.then(fn, fn)
  }

  then(onResolve, onReject) {
    return new MyPromise((resolve, reject) => {
      const resolveFn = (val) => {
        let res = onResolve && onResolve(val)
        if (res instanceof MyPromise) {
          res.then(resolve)
        } else {
          resolve(res)
        }
      }
      this.resolveQueue.push(resolveFn)
      let onReject = (err) => { // reject状态没有.then方法
        rejectFn && rejectFn(err)
        reject(err)
      }
      this.rejectQueue.push(onReject)
    })
  }
}
