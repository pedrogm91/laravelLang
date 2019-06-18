# laravelLang
create multilingual translated laravel
# Laravel 5.8

# Middleware Lang.php
`
<?php

namespace App\Http\Middleware;

use Closure;
use App\Location;

class Lang
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        /*
        *  
        */
        $locations = \App\Location::get()->pluck('location')->contains($request->segment(1));
        
        if ( !empty( session('locale') ) ){
            if ($locations) {
                \App::setLocale($request->segment(1));
                    session(['locale' => $request->segment(1)]);
            }else{
                \App::setLocale(session('locale'));
            }
        }else{
            if ($locations) {
                \App::setLocale($request->segment(1));
                    session(['locale' => $request->segment(1)]);
            }else{
                \App::setLocale($request->defaultLocale);
            }
        }

        return $next($request);
    }
}
`

# routes Web.php
`
Route::get('/{locale?}', 'WelcomeController@index')->name('home');
Route::get('/{locale?}/page/blog', function($locale = 'en'){
    return  session('locale');
});

Route::get('locale/{locale}', function($locale){
    session(['locale' => $locale]);
    return redirect()->route('home',[$locale]);
});

`
# Database migrations
`
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateLocationsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('locations', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('location');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('locations');
    }
}

`
# view welcome.blade.php
`
<div class="content">
    <div class="title m-b-md">
        @lang('messages.welcome',['name' => 'Pedro'])
    </div>

    <div class="links">
        <a href="{{ url('locale', ['en']) }}">EN</a>
        <a href="{{ url('locale', ['es']) }}">ES</a>
        <a href="{{ route('home', [session('locale')]) }}">Home {{ App::getLocale() }}</a>
    </div>
    <center>
        @if(session('locale'))
            <small>LangSession: {{ session('locale') }}</small>
        @else
            <small>LangSession: {{ session('locale') ?? 'null' }}</small>
        @endif
    </center>
    {{ dd(request()) }}
</div>
`
