<script src="https://cbt.smpn2majene.sch.id//assets/app/js/jquery.backstretch.js"></script>
<script type="text/javascript">
    let base_url = 'https://cbt.smpn2majene.sch.id/';
    var img = ["wall1.jpg", "wall2.png", "wall3.jpg"];

    $.backstretch([
        base_url + 'assets/img/' + img[0],
        base_url + 'assets/img/' + img[1],
        base_url + 'assets/img/' + img[2]
    ], {
        fade: 1000,
        duration: 10000
    });

    $(document).ready(function(){
        $('#myCarousel').carousel({
            interval: 1000 * 2,
            pause: 'none'
        });

        $('form#login input').on('change', function(){
            $(this).parent().removeClass('has-error');
            $(this).next().next().text('');
        });

        $('form#login').on('submit', function(e){
            e.preventDefault();
            e.stopImmediatePropagation();

            var infobox = $('#infoMessage');
            infobox.addClass('info-box align-items-center justify-content-center bg-gradient-info').text('Checking...');

            var btnsubmit = $('#submit');
            btnsubmit.attr('disabled', 'disabled').val('Wait...');

            const arrForm = $(this).serializeArray()
            const cbtOnly = arrForm.find(function (obj) {
                return obj.name === 'cbt-only'
            })
            localStorage.setItem('garudaCBT.login', cbtOnly !== undefined ? '1' : '0')

            $.ajax({
            url: $(this).attr('action'),
            type: 'POST',
            data: $(this).serialize(),
            success: function(data){
                infobox.removeAttr('class').text('');
                btnsubmit.removeAttr('disabled').val('Login');
                console.log('login', data);
                if(data.status){
                    infobox.addClass('info-box align-items-center justify-content-center bg-gradient-success').text('Login Sukses');

                    const isLogin = localStorage.getItem('garudaCBT.login')
                    const isCbtMode = isLogin ? isLogin === '1' : false
                    let go = base_url + data.url;
                    if (isCbtMode && data.role === 'siswa') {
                        go = 'siswa/cbt'
                    }
                    window.location.href = go;
                }else{
                    if(data.invalid){
                        $.each(data.invalid, function(key, val){
                        $('[name="'+key+'"').parent().addClass('has-error');
                        $('[name="'+key+'"').next().next().text(val);
                        if(val == ''){
                            $('[name="'+key+'"').parent().removeClass('has-error');
                            $('[name="'+key+'"').next().next().text('');
                        }
                        });
                    }
                        if(data.failed){
                            infobox.addClass('info-box align-items-center justify-content-center bg-gradient-danger').text(data.failed);
                        }
                    }
                }
            });
        });

        $('#toggle-password').on('click', function (e) {
            // toggle the type attribute
            const type = $('#password').attr('type') === 'password' ? 'text' : 'password';
            $('#password').attr('type', type);
            // toggle the eye / eye slash icon
            $(this).toggleClass('fa-eye-slash fa-eye');
        });
    });
</script>
<script src="https://cbt.smpn2majene.sch.id//assets/plugins/bootstrap/js/bootstrap.bundle.min.js"></script>
<script src="https://cbt.smpn2majene.sch.id//assets/adminlte/dist/js/adminlte.min.js"></script>
</body>
</html>
