FROM php:8.3-apache

# composer を追加
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# Apacheの mod_rewrite を有効化する
# CakePHPのURL（/test/view/1 みたいな）を動かすのに必要
RUN a2enmod rewrite

# apt-get install のときに 対話プロンプト（質問）を出さずに自動で進める設定
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    # PHPの intl 拡張（国際化、日付・通貨・照合など）をビルドするため
    libicu-dev \
    # PHPの zip 拡張をビルドするため
    libzip-dev \
    # 圧縮系の土台。zip周りで必要になりやすい
    zlib1g-dev \
    # PHPの mbstring（マルチバイト文字）で使う正規表現エンジン系の依存
    libonig-dev \
    # zipを展開するコマンド
    unzip \
    # 依存取得や開発用
    git \
  # PHP拡張を有効化してる本体
  && docker-php-ext-install intl mbstring pdo_mysql zip \
  # aptのキャッシュやパッケージ一覧を消して Dockerイメージを軽くする
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

# DocumentRoot を CakePHP の webroot に
# Apacheの設定を書き換えて、公開ディレクトリを CakePHPの webrootに向ける
RUN sed -ri -e 's!/var/www/html!/var/www/html/webroot!g' /etc/apache2/sites-available/000-default.conf \
 && sed -ri -e 's!/var/www/!/var/www/html!g' /etc/apache2/apache2.conf
