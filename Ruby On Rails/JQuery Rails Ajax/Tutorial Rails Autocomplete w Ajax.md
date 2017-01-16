# Rails Autocomplete with Ajax

## Lesson 1

Create new app

Create scaffold data

	rails g controller data enter search

Cek url 

	rake routes

	data_enter  GET    /data/enter(.:format)                  data#enter
	data_search GET    /data/search(.:format)                 data#search
	
Perhatikan methode GET pada ke duanya

Cek controller

	class DataController < ApplicationController
	  def enter
	  end

	  def search
	  end
	end

Cek view > data > enter.html.erb & search.html.erb

Edit like below:

	<h1>Search here</h1>
	<%= form_tag data_search_path do %>
		<%= text_field_tag :search, '', size:25 %>
		<%= submit_tag 'Search', :id => 'search_key' %>	
	<% end %>

Cek at localhost:3000

Cek form dengan inspect element

	<form action="/data/search" accept-charset="UTF-8" method="post">
		<input name="utf8" value="✓" type="hidden"><input name="authenticity_token" value="NcF5AlW+9SAZoDYbXxr77inbaAbnGnYaE0UaaHRH2CUUI0TbEUprQ66fY823FjQvAD6MJx+ZwGK5XGeEsO+myw==" type="hidden">
		<input name="search" id="search" value="" size="25" type="text">
		<input name="commit" value="Search" id="search_key" data-disable-with="Search" type="submit">
	</form>

Secara default form akan di generate dengan method POST. 

Maka form di atas kita update dengan method: 'GET', method:'GET'

	<%= form_tag data_search_path, method:'get' do %>
		<%= text_field_tag :search, '', size:25 %>
		<%= submit_tag 'Search', :id => 'search_key' %>	
	<% end %>

Cek lagi dengan inspect element maka akan berubah menjadi get.

	<form action="/data/search" accept-charset="UTF-8" method="get">
		<input name="utf8" value="✓" type="hidden">
		<input name="search" id="search" value="" size="25" type="text">
		<input name="commit" value="Search" id="search_key" data-disable-with="Search" type="submit">
	</form>	

Bila kita click search maka di browser akan tertulis seperti ini:

	http://localhost:3000/data/search?utf8=%E2%9C%93&search=&commit=Search

Dan pada terminal akan tertulis seperti ini:

	Started GET "/data/search?utf8=%E2%9C%93&search=&commit=Search" for 127.0.0.1 at 2017-01-09 13:48:44 +0700
	Processing by DataController#search as HTML
	  Parameters: {"utf8"=>"✓", "search"=>"", "commit"=>"Search"}
	  Rendering data/search.html.erb within layouts/application
	  Rendered data/search.html.erb within layouts/application (0.6ms)
	Completed 200 OK in 64ms (Views: 62.7ms | ActiveRecord: 0.0ms)

Perhatikan parameter search => kosong, bila kita isi ali misalnya

Di browser:

	http://localhost:3000/data/search?utf8=%E2%9C%93&search=Ali&commit=Search

Di terminal: 

	Started GET "/data/search?utf8=%E2%9C%93&search=Ali&commit=Search" for 127.0.0.1 at 2017-01-09 13:53:52 +0700
	Processing by DataController#search as HTML
	  Parameters: {"utf8"=>"✓", "search"=>"Ali", "commit"=>"Search"}
	  Rendering data/search.html.erb within layouts/application
	  Rendered data/search.html.erb within layouts/application (0.4ms)
	Completed 200 OK in 69ms (Views: 67.9ms | ActiveRecord: 0.0ms)

Untuk menghilangkan autocomplete yang dibuat oleh browser cookies, tambahkan di field input:

	:autocomplete => :off

Tambahkan remote true di form untuk menggunakan ajax.

	remote: true

Akan menjadi seperti dibawah ini:

	<%= form_tag data_search_path, method: 'GET', remote: true do %>
		<%= text_field_tag :search, '', size:25, :autocomplete => 'off' %>
		<%= submit_tag 'Search', :id => 'search_key' %>
	<% end %>	

Apabila kita click search maka tidak akan pindah halaman lagi

Bila kita lihat log di terminal maka, search as JS:

	Started GET "/data/search?utf8=%E2%9C%93&search=&commit=Search" for 127.0.0.1 at 2017-01-09 14:14:54 +0700
	Processing by DataController#search as JS
	  Parameters: {"utf8"=>"✓", "search"=>"", "commit"=>"Search"}
	  Rendering data/search.html.erb within layouts/application
	  Rendered data/search.html.erb within layouts/application (0.5ms)
	Completed 200 OK in 61ms (Views: 57.2ms | ActiveRecord: 0.0ms)

Buat file baru search.js.erb, yang akan memproses search tsb

Kita test dengan alert:

	alert("Hello from search.js.erb!")

![search](http://res.cloudinary.com/medioxtra/image/upload/v1483946451/Screenshot_from_2017-01-09_14_19_49_ffu1e0.png)

## Lesson 2

Cek jQueryUI [autocomplete](https://jqueryui.com/autocomplete/)

Click view source:

Download source nya yang ada disitu:

	<link rel="stylesheet" href="//code.jquery.com/ui/1.12.1/themes/base/jquery-ui.css">
	<link rel="stylesheet" href="/resources/demos/style.css">
	<script src="https://code.jquery.com/jquery-1.12.4.js"></script>
	<script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

Dengan cara copy link di layout > applications.html.erb

 	<!-- autocomplete -->
    <link rel="stylesheet" href="//code.jquery.com/ui/1.12.1/themes/base/jquery-ui.css">
    <script src="https://code.jquery.com/jquery-1.12.4.js"></script>
    <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

Edit file search.js.erb

	$( function() {
	  var availableTags = [
	  "ActionScript",
	  "AppleScript",
	  "Asp",
	  "BASIC",
	  "C",
	  "C++",
	  "Clojure",
	  "COBOL",
	  "ColdFusion",
	  "Erlang",
	  "Fortran",
	  "Groovy",
	  "Haskell",
	  "Java",
	  "JavaScript",
	  "Lisp",
	  "Perl",
	  "PHP",
	  "Python",
	  "Ruby",
	  "Scala",
	  "Scheme"
	  ];
	  $( "#search" ).autocomplete({
	  source: availableTags
	  });
	} );

Modif file search.js.erb menjadi

	$( function() {
	  var availableTags = <%= raw @auto %>;
	  $( "#search" ).autocomplete({
	  source: availableTags
	  });
	} );

Untuk mengambil data dari database gunakan:

	<%= raw @auto %>

dimana instan variable @auto di inisiasi di controler.		

Modif di controller 

	def search
	    @auto = [
	      "ActionScript",
	      "AppleScript",
	      "Asp",
	      "BASIC",
	      "C",
	      "C++",
	      "Clojure",
	      "COBOL",
	      "ColdFusion",
	      "Erlang",
	      "Fortran",
	      "Groovy",
	      "Haskell",
	      "Java",
	      "JavaScript",
	      "Lisp",
	      "Perl",
	      "PHP",
	      "Python",
	      "Ruby",
	      "Scala",
	      "Scheme"
	    ]
	end

Kemudian cek di browser, akan sama hasilnya.

Modif di controller agar dapat mengambil data dari server.

	def search
		query ="%" + params[:search] + "%"
		@auto = Koman.where('name LIKE ?', query).pluck(:name)
	end

Dan coba di browser, sukses!

![sukses](http://res.cloudinary.com/medioxtra/image/upload/v1483954360/sukses_ycbgat.png)	

## Lesson 3

Tambahkan puts @auto.inspect untuk inspect di terminal

	def search
		query ="%" + params[:search] + "%"
		@auto = Koman.where('name LIKE ?', query).pluck(:name)
		puts @auto.inspect
	end

![inspek](http://res.cloudinary.com/medioxtra/image/upload/v1483959982/inspect_km16xv.png)

	Started GET "/data/search?utf8=%E2%9C%93&search=b&commit=Search" for 127.0.0.1 at 2017-01-09 18:07:05 +0700
	Processing by DataController#search as JS
	  Parameters: {"utf8"=>"✓", "search"=>"b", "commit"=>"Search"}
	   (0.3ms)  SELECT "komen"."name" FROM "komen" WHERE (name LIKE '%b%')
	["Brandon JB", "Dyo B"]
	  Rendering data/search.js.erb
	  Rendered data/search.js.erb (0.8ms)
	Completed 200 OK in 24ms (Views: 15.8ms | ActiveRecord: 0.3ms)

**pluck(*column_names) public**

Use pluck as a shortcut to select one or more attributes without loading a bunch of records just to grab the attributes you want.

	Person.pluck(:name)

instead of

	Person.all.map(&:name)

Tambahkan script di assets/javascripts/application.js

	$(function(){
	  $('#search').on('keyup', function(){
	    $('#search_key').click();
	  });
	});

*Catatan karena sementara belum jalan, maka saya taruh di file **enter.html.erb** and works perfectly.

![](http://res.cloudinary.com/medioxtra/image/upload/c_scale,w_900/v1484012610/onkeyup_zw4qwl.png)

** *Catatan: Untuk mencegah input kosong atau spasi tambahkan variable dan condition:**

	$(function(){
	  $('#search').on('keyup', function(){
	  	var x = ( $('#search').val() ).trim();
	  	if (x.length > 0){
	    	$('#search_key').click();
	    }
	  });
	});

## Lesson 4