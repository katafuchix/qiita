<!--
title:   Rails 6 Grapeを利用したAPI作成、Swaggerでの確認
tags:    Rails,Ruby
id:      eceafb5788a9ec60c3dd
private: false
-->
# RoRでのAPI作成メモ　
ruby 2.7.0 & Rails 6 
Rails 5.x 系の資源を利用する例です。
https://qiita.com/katafuchix/items/8a96e2fa9ddc8bc83545

## プロジェクト作成
`
$ mkdir api_sample
$ cd api_sample
$ bundle init
`
Gemfile編集

```ruby
# frozen_string_literal: true

source "https://rubygems.org"

git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

gem 'rails'
```

インストール
*MySQLを利用する例

```
$ bundle install --path vendor/bundle
$ bundle exec rails new . --skip-action-mailer --skip-active-storage --skip-action-cable -d mysql
```

Gemfileに追加

```Gemfile
# API
gem 'grape'
gem 'hashie-forbidden_attributes'
gem 'grape-jbuilder'
gem 'grape_on_rails_routes'
gem 'swagger-ui_rails'
gem 'grape-swagger'
gem 'grape-swagger-rails'
```
もう一度 bundle install を実行してエラーがないことを確認する


## モデルクラス準備、データ投入

```
$ bin/rails g model Task name:string description:string
```
db/seed.rb

```seed.rb
Task.create(name: 'タスク1', description: 'サンプルタスク1')
Task.create(name: 'タスク2', description: 'サンプルタスク2')
Task.create(name: 'タスク3', description: 'サンプルタスク3')
```

`
$ bundle exec rake db:create
$ bundle exec rake db:migrate
$ bundle exec rake db:seed
`

## GrapeによるAPI作成

### ルーティング
http://localhost:3000/api/v1/tasks/displays とかでアクセスしたいので各種設定
RailsかGrapeの仕様で「app/api」というディレトクリ以下のパス、ファイル名、クラス名が一致しなければならない。
それまでのRailsで「api/versions/v1」という作りをしている場合は、「api/api/versions/v1」というパスが二重になったフォルダ構成となってしまう。
ここでは以前のシステムを最小限度の手順で最新のRailsで組み直すため、ここはこのままで進めます。

```config/routes.rb
Rails.application.routes.draw do
  mount Versions::V1::Api => '/'
  mount GrapeSwaggerRails::Engine => '/api/swagger'
end
```

### APIディレクトリの作成
プロジェクト直下に api フォルダを作成してそこにAPIのプログラムを配置するために for Grape 以下の記述を追加

```config/application.rb
require_relative 'boot'

require 'rails/all'

# Require the gems listed in Gemfile, including any gems
# you've limited to :test, :development, or :production.
Bundler.require(*Rails.groups)

module ApiSample
  class Application < Rails::Application
    # Initialize configuration defaults for originally generated Rails version.
    config.load_defaults 6.0

    # Settings in config/environments/* take precedence over those specified here.
    # Application configuration can go into files in config/initializers
    # -- all .rb files in that directory are automatically loaded after loading
    # the framework and any gems in your application.

    # For Grape
    config.paths.add File.join('app', 'api'), glob: File.join('**', '*.rb')
    config.autoload_paths += Dir[Rails.root.join('app', 'api', '*')]
    config.middleware.use(Rack::Config) do |env|
      env['api.tilt.root'] = Rails.root.join 'app', 'views', 'api'
    end
  end
end
```

`
$ mkdir app/api
$ mkdir app/api/api
$ mkdir app/api/api/versions
$ mkdir app/api/api/versions/v1
`


### API作成
大元にこういうのを作成してその中でエンドポイント単位のモジュールをincludeするのが管理しやすい
/api/vi/〜のパスにするために内部でこんな風に記述
テンプレートはJbuilderを指定

```app/api/api/versions/v1/api.rb
module Versions
  module V1
    class Api < Grape::API
      version 'v1', using: :path
      format :json
      formatter :json, Grape::Formatter::Jbuilder
      prefix :api

      include ::Versions::V1::TaskDisplays

      # :nocov:
      if Rails.env.development? 
        add_swagger_documentation add_version: true
      end
      # :nocov:
    end
  end
end
```


各APIのコードはこんな感じ。　命名規則に注意。

```app/api/api/versions/v1/task_displays.rb
module Versions
  module V1
    module TaskDisplays
      extend ActiveSupport::Concern
      included do
        namespace :tasks do
          namespace :displays do
            desc 'タスク一覧を取得する'
            get '', jbuilder: 'v1/task_displays/index' do
              @tasks = Task.all
            end
          end
        end
      end
    end
  end
end
```

テンプレードはこんな感じ

```app/views/api/v1/task_displays/index.jbuilder
json.tasks @tasks do |task|
   json.(task, :id, :name, :description)
end
```
swagger設定ファイル

```config/initializers/grape_swagger_rails.rb
unless Rails.env.production?
  GrapeSwaggerRails.options.app_name = 'Grape API Sample'
  GrapeSwaggerRails.options.app_url  = '/'
  GrapeSwaggerRails.options.url = 'api/v1/swagger_doc.json'
end
```

swagger用にjs, cssをimportするために2行追加

```app/assets/config/manifest.js
//= link_tree ../images
//= link_directory ../stylesheets .css

// 下２行追加
//= link grape_swagger_rails/application.css
//= link grape_swagger_rails/application.js
```

## 確認
railsを起動してブラウザで確認

```
bin/rails s
```

### API
http://localhost:3000/api/v1/tasks/displays
<img width="726" alt="スクリーンショット 2020-03-15 2.02.32.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/266239/2be4b23c-4d46-7a53-ad21-d4e7f06c5998.png">

### swagger
http://localhost:3000/api/swagger
<img width="1191" alt="スクリーンショット 2020-03-15 2.02.15.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/266239/da688965-d467-8724-9bc7-ff3a38cf5fb7.png">

あとは ここと同じように・・・
<a href="https://qiita.com/katafuchix/items/705d628e09cf877e61de">Rails 5.2 Grapeを利用したAPI作成、Swaggerでの確認　続編</a>

<a href="https://github.com/katafuchix/grape_sample">サンプル</a>