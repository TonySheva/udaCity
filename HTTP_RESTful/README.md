POST = 新增
GET = 讀取
PUT = 更新
DELETE = 刪除
後來在設計 API only 的 Web service 時，常常搞不清楚到底要用 PUT 還是 POST，才發現我被 Rails 的鷹架範例誤導了(被框架框住想法了?)，所謂的 PUT 其實也可以用到新增，而且還有一個蠻新的 HTTP Verb 叫做 PATCH，像 Github API 和 Rails 4 都開始採用。

PUT 比較正確的定義是 Replace (Create or Update)，例如PUT /items/1的意思是替換/items/1，如果已經存在就替換，沒有就新增。PUT 必須包含items/1的所有屬性資料。

但是這個行為似乎不怎麼好用，如果只是為了更新items/1的其中一個屬性，就需要重傳所有items/1的屬性也太浪費頻寬了，所以後來又有新的 PATCH Method 標準，可以用來做部分更新(Partial Update)。

用幾個 Ruby code 來舉例吧：

POST 新增：

# POST /items
def create
  @item = Item.new
  @item.attributes = { :name => params[:name],
                      :image => params[:image] }
  @item.save
end
PUT 替換(新增或完整更新)，此例中如果image參數沒有傳，會被更新成空：

# PUT /items/{id}
def replace
   @item = Item.find_by_id(params[:id])

  unless @item  # if @item.nil?
      @item = Item.new
      @item.id = params[:id]
  end

  @item.attributes = { :name => params[:name],
                       :image => params[:image] }
  @item.save
end
PATCH 部分更新，此例中如果image參數沒有傳，就不會被更新：

# PATCH /items/{id}
def patch
   @item = Item.find(params[:id])
  @item.attributes = params.slice(:name, :image)
  @item.save
end
DELETE 刪除，此例中無論如何 items/1 最後都不存在了

# DELETE /items/{id}
def destroy
   @item = Item.find_by_id(params[:id])
  @item.destroy if @item
end
有時候拘泥於”語意”這件事情不容易想清楚設計 REST API 時要用哪一個 HTTP 方法，因為有時候不一定是CRUD的形式。我認為 9.1 Safe and Idempotent Methods 定義中的 “Idempotent” 特性蠻實用的。idempotent 的意思是如果相同的操作再執行第二遍第三遍，結果還是跟第一遍的結果一樣 (也就是說不管執行幾次，結果都跟只有執行一次一樣)。根據 HTTP 的規格，GET, HEAD, PUT 和 DELETE 是 idempotent，相同的 Request 再執行一次，結果還是一樣。只有 POST 和 PATCH 不是 idempotent，POST 再執行一遍，會再新增一筆資料，PATCH 則是有不能保證 idempotent 的可能性(徵求例子)。POST 和 PATCH 都不是 idempotent 的操作，這也是為什麼 Github API 裡用 POST 當做 PATCH 的相容取代方案。

另一個 HTTP Methods 特性是”Safe”，這比較簡單，只有 GET 和 HEAD 是 Safe 操作。Safe 特性會影響是否可以快取(POST/PUT/PATCH/DELETE 一定都不可以快取)。而 Idempotent 特性則是會影響可否 Retry (重試，反正結果一樣)。

SAFE?	IDEMPOTENT?
GET	Y	Y
POST	N	N
PATCH	N	N
PUT	N	Y
DELETE	N	Y
透過 Idempotent 的特性，有時候可以幫助你判斷該用哪一個 HTTP Methods。回到前面講 PUT 好像不太好用，例如以瀏覽器為主的 HTML 應用表單，要麻是 POST 新增資料，要麻就是用 PATCH 部分更新已經存在的資料(你不會希望用 PUT 修改個人資料的時候，每次都要重傳照片吧)，因此比較少用到 PUT。這也是為什麼 Rails 4 把表單修改的 PUT 改成 PATCH Method，透過 Rails 鷹架產生出來的 update，其實符合的是 PATCH 行為而不是 PUT。

不過還是有一些我認為蠻適合用PUT的情境，例如訂閱東西該用POST還是PUT呢?

POST /subscriptions
# 還是
PUT /subscriptions/{something_id}
訂閱東西只有兩個狀態，”已訂閱”或”沒有訂閱”，這個訂閱的操作再重送幾次，還是”已訂閱”，所以我認為蠻符合 PUT 的 idempotent 特性。而對應的取消訂閱 API 想當然就是

DELETE /subscriptions/{something_id}
另外一個我覺得有趣又實用的 PUT 例子是，設計 API 给可以離線 offline 使用的行動裝置(例如iPhone)。支援 offline 產生的資料，通常會使用 UUID 來產生 ID，這樣就不需要透過中央伺服器管控 ID，方便裝置之間的同步。這樣的情境下，新增資料的 REST API 其實可以提供兩種：

POST /items # 參數帶 uuid=4937973d-e349-460a-a6ad-38625125b24a。如果不帶uuid則由server來產生uuid
# 和
PUT /items/4937973d-e349-460a-a6ad-38625125b24a
對行動裝置的 client 來說，用POST的問題在於離線環境的不穩定，有可能POST之後沒有收到回傳，因此行動裝置不確定有沒有同步成功，這時候要再重試(retry)，但是用 POST 就爆炸了，因為 server 會再新建一筆 uuid 重複的資料。但是用 PUT 就沒有問題了，PUT 是 Idempotent 的操作，可以重送沒有關係 (可以再搭配 Conditional PUT 的機制，用 ETag 和 Last-Modified Headers 確保沒有覆蓋衝突)

如果是沒有 offline 需求的 client，例如第三方應用，那麼就可以用 POST /items 這個 API，交由 server 來產生 uuid。

app.js :

        task = {
            "celery_task_name": "facebook.search_account_custom_audience",
            "args": ["act_" + $scope.customer.id],
            "wait": 0.8 , // 0.8s
            "meta": {
                "account_id": $scope.getLastUsableFbAccount()
            },
            "method": FB_TASK_METHOD.get,
        }
        $api.targetingSearch(task, function (results) {
            $scope.audienceResults = results;
        }, function (data) {
            console.log(data);
        })

api.js:
            facebookTaskApi: function(task, handler, fail) {
                Taskfactory(task).success(handler).error(fail).resolve();
            },

        // create celery task
        function Taskfactory(task) {
            task = new makeTask(task);
            return task;
        };

        function makeTask(task) {
            this._defaultInterval = 100;
            this._wait = 2;  // 2 second
            this.retryCount = 10;
            this.count = 0;
            this.interval = 0;
            this.task = task;
            this.org_task = task;
        };

        makeTask.prototype.periodHandler = function(responseTask) {
            if (this.count < this.retryCount) {
                responseTask["method"] = FB_TASK_METHOD.get;
            } else if (this.count == this.retryCount) {
                responseTask["wait"] = this._wait;
                responseTask["method"] = FB_TASK_METHOD.get;
            } else {
                this.errorHandler({
                    "state": "FAILURE",
                    "status": "Client Time Out",
                });
                return;
            }
            this.task = responseTask;
            $timeout(this.resolve.bind(this), this.interval);
            this.count += 1
            this.interval += this.count * this._defaultInterval;
        };

        makeTask.prototype.responseHandler = function(data) {
            if (data.state === TASK_STATUS.success) {
                this.successhandler(data.results);
            } else if (data.state === TASK_STATUS.failure) {
                this.errorHandler(data);
            } else {
                this.periodHandler(data);
            }
        };

        makeTask.prototype.resolve = function () {
            if (this.task.method == FB_TASK_METHOD.get) {
                $http.get(
                    "/api/facebook/task", {
                        params: { data: this.task }
                    }
                ).success(
                    this.responseHandler.bind(this)
                    ).error(
                    this.errorHandler.bind(this)
                    );
            } else if(this.task.method == FB_TASK_METHOD.post) {
                $http.post(
                    "/api/facebook/task",
                    this.task
                ).success(
                    this.responseHandler.bind(this)
                    ).error(
                    this.errorHandler.bind(this)
                    );
            }
        };


        makeTask.prototype.success = function(handler) {
            this.successhandler = handler;
            return this;
        };

        makeTask.prototype.error = function(handler) {
            this.errorHandler = handler;
            return this;
        };

router.go:
 	m.Group("/api/facebook", func(r martini.Router) {
		r.Get("/task", FacebookTaskHandler)
        }
fb.go:
func FacebookTaskHandler(r *http.Request, user models.User) (int, []byte) {
	if r.Method == "GET" {
		r.ParseForm()
		param := r.Form.Get("data")
		var taskParam Task
		err := json.Unmarshal([]byte(param), &taskParam)
		if err != nil {
			return HandleHttpError(
				400,
				err,
			)
		}
		return FacebookBaseTaskHandler(taskParam, user)
	}

	if r.Method == "POST" {
		// read task content
		defer r.Body.Close()
		contentByte, err := ioutil.ReadAll(r.Body)
		if err != nil {
			return HandleHttpError(
				400,
				err,
			)
		}

		if len(contentByte) == 0 {
			return HandleHttpError(
				400,
				errors.New("request invalid due to zero-length params"),
			)
		}

		// load task
		// task := map[string]interface{}{}
		var task Task

		json.Unmarshal(contentByte, &task)
		return FacebookBaseTaskHandler(task, user)
	}

	return HandleHttpError(
		400,
		errors.New("http request method is worng"),
	)
}
