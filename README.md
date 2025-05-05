# maui_dicas
dicas pra usar na plataforma .net maui

----------------------------------------------------------------------------------------------------------------------------------------

// mudar a MainPage em tempo de execução

// segunda() é a pagina a ser chamada

App.Current.MainPage = new Segunda();

----------------------------------------------------------------------------------------------------------------------------------------

// camera no .net maui

apague todos os arquivos de paginas desnecessarios
crie uma content page chamada de PagCamera

NUGET -> Importar a biblioteca CAMERA.MAUI

Precisamos dar permissoes para isso vamos usar o Manifest do Android
no projeto vá até Platforms -> Android -> AndroidManifest.xml

Em permissões deixe marcado as opções:

##### ACCESS_NETWORK_STATE
##### CAMERA
##### INTERNET
##### READ_EXTERNAL_STORAGE
##### READ_MEDIA_AUDIO
##### READ_MEDIA_IMAGES
##### READ_MEDIA_VIDEO
##### RECORD_AUDIO
##### WRITE_EXTERNAL_STORAGE

agora abra o Manifest como EDITOR de XML
insira o codigo abaixo no final

 <uses-permission android:name="android.permission.RECORD_VIDEO" />
 <queries>
   <intent>
     <action android:name="android.media.action.IMAGE_CAPTURE" />
   </intent>
 </queries>

Vamos em Platforms -> Android -> MainApplication.cs

logo abaixo dos Using, insira o codigo abaixo

##### [assembly: UsesPermission(Android.Manifest.Permission.ReadExternalStorage, MaxSdkVersion = 32)]
##### [assembly: UsesPermission(Android.Manifest.Permission.ReadMediaAudio)]
##### [assembly: UsesPermission(Android.Manifest.Permission.ReadMediaImages)]
##### [assembly: UsesPermission(Android.Manifest.Permission.ReadMediaVideo)]

##### // Needed for Taking photo/video
##### [assembly: UsesPermission(Android.Manifest.Permission.Camera)]
##### [assembly: UsesPermission(Android.Manifest.Permission.WriteExternalStorage, MaxSdkVersion = 32)]

##### // Add these properties if you would like to filter out devices that do not have cameras, or set to false to make them optional
##### [assembly: UsesFeature("android.hardware.camera", Required = true)]
##### [assembly: UsesFeature("android.hardware.camera.autofocus", Required = true)]
##### [assembly: UsesFeature("android.hardware.camera.front", Required = false)]

----------------------------------------------------------------------------------------------------------------------------------------

na raiz do nosso projeto vamos encontrar o arquivo MauiProgram.cs

logo abaixo de: .UseMauiApp<App>()

insira o codigo abaixo:

.UseMauiCameraView()

----------------------------------------------------------------------------------------------------------------------------------------

no pagCamera adicione:

---------------------------------------------------------------------------------------------
no cs
using Camera.MAUI;

dentro da classe (initial)

 public string Name;
 public string DeviceId;
 public CameraPosition Position;
 public bool HasFlashUnit;
 public float MinZoomFactor;
 public float MaxZoomFactor;
 public float HorizontalViewAngle;
 public float VerticalViewAngle;
 public List<Size> AvailableResolutions;
 public bool playing = false;


private void cameraView_CamerasLoaded(object sender, EventArgs e)
{
    if (cameraView.NumCamerasDetected > 0)
    {
        if (cameraView.NumMicrophonesDetected > 0)
            cameraView.Microphone = cameraView.Microphones.First();
        cameraView.Camera = cameraView.Cameras.First();
        MainThread.BeginInvokeOnMainThread(async () =>
        {
            if (await cameraView.StartCameraAsync() == CameraResult.Success)
            {
                controlButton.Text = "Stop";
                playing = true;
            }
        });
    }
}

private async void btnPararCamera(object sender, EventArgs e)
{
    await cameraView.StopCameraAsync();
}

private async void btnIniciarCamera(object sender, EventArgs e)
{
    await cameraView.StartCameraAsync();
}

private void btnTirarFoto(object sender, EventArgs e)
{
    cameraView_CamerasLoaded();
}





---------------------------------------------------------------------------------------------
no xaml:
xmlns:cv="clr-namespace:Camera.MAUI;assembly=Camera.MAUI"

<!-- visualizador da camera -->
<cv:CameraView 
    x:Name="cameraView" 
    WidthRequest="300" 
    HeightRequest="300"
    CamerasLoaded="cameraView_CamerasLoaded"/>


 <Button x:Name="controlButtonIniciar" Text="Ver Camera" Clicked="btnIniciarCamera"
   Margin="80,20,80,20"
   />
 
 <Button x:Name="controlButton" Text="Parar Camera" Clicked="btnPararCamera"
         Margin="80,20,80,20"
         />

 <Button x:Name="controlTirarFoto" Text="Tirar Foto" Clicked="btnTirarFoto"
         Margin="80,20,80,20"
         />




