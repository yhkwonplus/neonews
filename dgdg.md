html형식
<iframe width="560" height="315" src="https://www.youtube.com/embed/oZS74-w719E" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

다른방식
<div class="video_w">
	<iframe width="560" height="315" src="https://www.youtube.com/embed/3wRQW-W9Dio" frameborder="0" allowfullscreen></iframe>
</div>


영상 여러개 재생 &playlist=0OvdMVDIvDE0 추가

<div class="video_w">
	<iframe width="560" height="315" src="https://www.youtube.com/embed/3wRQW-W9Dio?autoplay=1&mute=1&start=110&end=120&controls=0&playlist=0OvdMVDIvDE0" frameborder="0" allowfullscreen></iframe>
</div>

반응형 (동적으로 크기조절)
<div class="video_w">
	<iframe width="100%" height="100%" src="https://www.youtube.com/embed/3wRQW-W9Dio?autoplay=1&mute=1&start=110&end=120&controls=0&loop=1&playlist=3wRQW-W9Dio&playsinline=1" frameborder="0" allowfullscreen></iframe>
</div>


md형식

[![IU(아이유) _ Into the I-LAND](http://img.youtube.com/vi/QYNwbZHmh8g/0.jpg)](https://youtu.be/QYNwbZHmh8g?t=0s)


접기펼치기
<details>
    <summary>Click Me!!!</summary>
    <div class="tpt">details 과 summary 그리고 css까지 적용</div>
</details>



<!doctype html>
<html>
    <head>
        <meta charset="utf-8">
        <title>유튜브 영상 넣기</title>
        <style type="text/css">
            
        </style>
    </head>
    <body>
        <iframe width="560" height="315" src="https://www.youtube.com/embed/iDjQSdN_ig8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </body>
</html>



	$(document).ready(function (){
		//youtube_list_do();
	});

	var youtube_player;
	function youtube_list_do(){
		var params = $('#frm_lyrics_info').serialize();
		$.ajax({
			type: 'POST',
			url: '/main/youtube_list_do',
			data: params,
			dataType: 'json',
			cache: false,
			async: true,
			success: function (ret) {
				if ( ret && (ret.code == 200 || ret.code == 203) ) {
					var search_list = ret.data.search_list;
					var search_list_cnt = search_list.length;
					if( search_list_cnt > 0 ){
						html = '';
						for(var i = 0; i < search_list_cnt; i++ ){
							var youtube_info = search_list[i];
							var first_class = '';
							var first_text_class = '';
							if( i == 0 ){
								youtube_player.cueVideoById(youtube_info.yid, 0, "default");
								first_class = 'active';
								first_text_class = 'text-white';
								$("#fli_played_id").val(youtube_info.yid);
								var og_img = youtube_info.thumbnails_high;
								if( og_img ){
									$("#meta_og_image").attr("content", og_img);
								}
							}

							html += '<li class="list-group-item '+first_class+'"'
								+ 'id="youtube_id_'+youtube_info.yid+'" '
								+ 'data-yp_youtube_title="'+encodeURIComponent(youtube_info.title)+'" '
								+ 'data-yp_thumbnails_default="'+encodeURIComponent(youtube_info.thumbnails_default)+'" '
								+ 'data-yp_thumbnails_medium="'+encodeURIComponent(youtube_info.thumbnails_medium)+'" '
								+ 'data-yp_thumbnails_high="'+encodeURIComponent(youtube_info.thumbnails_high)+'" '
								'>';
							html += '   <a href="javascript:;" '
								+ 'onclick="replay_youtube(\''+youtube_info.yid+'\')" '
								+ 'class="notify-item">';
							html += '       <div class="notify-thumb">';
							html += '           <img src="'+youtube_info.thumbnails_high+'" alt="">';
							html += '       </div>';
							html += '       <div class="notify-text">';
							html += '           <p class="'+first_text_class+'">'+youtube_info.title+'</p>';
							html += '       </div>';
							html += '   </a>';
							html += '</li>';

							if( i == 7 ){
								break;
							}
						}
						$("#youtube_list").html(html);
						$("#youtube_box").show();

						$('#youtube_list').slimScroll({
							height: '300px'
						});


					}
				}
			}
		});
	}

	function env_repeat_youtube(){
		var this_val = $("#fli_repeat").val();

		if( this_val == "N" ){
			$("#btn_repeat_youtube").addClass("btn-outline-danger");
			$("#btn_repeat_youtube").removeClass("btn-outline-primary");
			$("#btn_repeat_youtube").html('<i class="fa fa-square text-green"></i> Repeat');
			$("#btn_repeat_youtube").trigger("blur");

			$("#fli_repeat").val("Y");
		} else {
			$("#btn_repeat_youtube").addClass("btn-outline-primary");
			$("#btn_repeat_youtube").removeClass("btn-outline-danger");
			$("#btn_repeat_youtube").html('<i class="fa fa-square text-gray"></i> Repeat');

			$("#btn_repeat_youtube").trigger("blur");
			$("#fli_repeat").val("N");
		}
	}

	function repeat_youtube(){
		ended_youtube('');

		if( youtube_player && $("#fli_repeat").val() == "Y" ){
			youtube_player.loadVideoById($("#fli_played_id").val(), 0, "default");
		}
	}

	function replay_youtube(yid){
		$("#youtube_list li").removeClass("active");
		$("#youtube_list .notify-text p").removeClass("text-white");
		if( youtube_player ){
			$("#youtube_id_"+yid).addClass("active");
			$("#youtube_id_"+yid+" .notify-text p").addClass("text-white");
			youtube_player.loadVideoById(yid, 0, "default");
			$("#fli_played_id").val(yid);
		}
	}

	function ended_youtube(ended_type){

		var yp_youtube_id = $("#fli_played_id").val();
		var li_youtube = $("#youtube_id_"+yp_youtube_id);

		var lyrics_no = $("#fli_p").val();
		var yp_youtube_title = decodeURIComponent(li_youtube.data('yp_youtube_title'));
		var yp_thumbnails_default = decodeURIComponent(li_youtube.data('yp_thumbnails_default'));
		var yp_thumbnails_medium = decodeURIComponent(li_youtube.data('yp_thumbnails_default'));
		var yp_thumbnails_high = decodeURIComponent(li_youtube.data('yp_thumbnails_default'));

		create_user_youtube_playlist_do(lyrics_no, yp_youtube_title, yp_youtube_id,
		yp_thumbnails_default,yp_thumbnails_medium,yp_thumbnails_high, function(ret) {
			if ( ret && (ret.code == 200 || ret.code == 203) ) {
				//console.log('재생목록에 저장 완료');
				if( ended_type == 'm' ){
					alert('Saved to playlist');
				}
			} else {
				//console.log('재생목록에 저장 실패');
			}
		});
		return true;
	}

 