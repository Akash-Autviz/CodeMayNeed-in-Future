# CodeMayNeed-in-Future
import { FontAwesome, MaterialCommunityIcons } from "@expo/vector-icons";
import React, { useEffect, useState } from "react";
import * as SecureStore from "expo-secure-store";
// import adaptive from "../assets/images/adaptive-icon.png";
import { StackActions, useNavigation } from "@react-navigation/native";
import {
  TouchableOpacity,
  ImageBackground,
  Image,
  ScrollView,
  Dimensions,
  StyleSheet,
  BackHandler,
  Share,
  TextInput,
  KeyboardAvoidingView,
  Platform,
  Keyboard,
  PermissionsAndroid,
  TouchableWithoutFeedback,
} from "react-native";
import * as DocumentPicker from "expo-document-picker";
import Toast from "react-native-toast-message";
import { View, Text } from "../components/Themed";
import { useStateContext } from "./Context/ContextProvider";
import axios from "axios";
import { baseUrl, header } from "../utils";

import moment from "moment";

import ImagePicker, {
  launchCamera,
  launchImageLibrary,
} from "react-native-image-picker";
const wid = Dimensions.get("window").width;
const high = Dimensions.get("window").height;
declare global {
  interface FormData {
    getHeaders: () => { [key: string]: string };
  }
}
export default function EditProfile(props: any) {
  FormData.prototype.getHeaders = () => {
    return { "Content-Type": "multipart/form-data" };
  };
  const { userDetail, userImage, setuserImage, setUserDetail, access_token } =
    useStateContext();
  const navigation = useNavigation();
  const [currUserDetail, setcurrUserDetail] = useState<any>();
  const [surName, setSurName] = useState("");
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [phoneNumber, setPhoneNumber] = useState("");

  const [image, setImgae] = useState<String>("");
  let header: any = {
    "Content-Type": "multipart/form-data",
    "Abp-TenantId": "1",
    Authorization: `Bearer ${access_token}`,
  };

  const uploadImage = async (file: any) => {};

  const getImageFromLibrary = async () => {
    try {
      const grantedcamera = await PermissionsAndroid.request(
        PermissionsAndroid.PERMISSIONS.CAMERA,
        {
          title: "App Camera Permission",
          message: "App needs access to your camera ",
          buttonNeutral: "Ask Me Later",
          buttonNegative: "Cancel",
          buttonPositive: "OK",
        }
      );
      const grantedstorage = await PermissionsAndroid.request(
        PermissionsAndroid.PERMISSIONS.WRITE_EXTERNAL_STORAGE,
        {
          title: "App Camera Permission",
          message: "App needs access to your camera ",
          buttonNeutral: "Ask Me Later",
          buttonNegative: "Cancel",
          buttonPositive: "OK",
        }
      );
      if (
        grantedcamera === PermissionsAndroid.RESULTS.GRANTED &&
        grantedstorage === PermissionsAndroid.RESULTS.GRANTED
      ) {
        console.log("Camera & storage permission given");

        var options: any = {
          maxHeight: 200,
          maxWidth: 200,
          selectLimit: 1,
          mediaType: "photo",
          includeBase64: false,
        };
        var otherOptions: any = {
          mediaTypes: " photo",
          allowsEditing: true,
          includeBase64: false,
          quality: 1,
        };
        let res: any = await DocumentPicker.getDocumentAsync({});
        // const res: any = await launchImageLibrary(otherOptions, (res: any) => {
        //   console.log("Response = ", res);

        //   if (res.didCancel) {
        //     console.log("User cancelled image picker");
        //   } else if (res.error) {
        //     console.log("ImagePicker Error: ", res.error);
        //   } else if (res.customButton) {
        //     console.log("User tapped custom button: ", res.customButton);
        //     alert(res.customButton);
        //   } else {
        //     const source = { uri: res.uri };
        //     console.log(source);
        //   }
        // });
        console.log("afterALl", res);
        setImgae(res.uri);
        const payload = new FormData();
        payload.append("file", {
          name: res.uri,
          type: res.mimeType,
          uri: res.uri,
        });
        var config = {
          method: "post",
          maxBodyLength: Infinity,
          url: "http://13.126.218.96/api/services/app/CommonService/UploadImage",
          headers: {
            Authorization: `Bearer ${access_token}`,
            "Content-Type": "multipart/form-data",
          },
          data: payload,
        };
        await axios(config)
          .then(function (response) {
            console.log(JSON.stringify(response.data));
          })
          .catch(function (error) {
            console.log("fdasfdsa", error);
          });
      } else {
        console.log("Camera permission denied");
      }
    } catch (error) {
      console.log(error);
    }
  };
  // const launchCameraa = async () => {
  //   const grantedcamera = await PermissionsAndroid.request(
  //     PermissionsAndroid.PERMISSIONS.CAMERA,
  //     {
  //       title: "App Camera Permission",
  //       message: "App needs access to your camera ",
  //       buttonNeutral: "Ask Me Later",
  //       buttonNegative: "Cancel",
  //       buttonPositive: "OK",
  //     }
  //   );
  //   const grantedstorage = await PermissionsAndroid.request(
  //     PermissionsAndroid.PERMISSIONS.WRITE_EXTERNAL_STORAGE,
  //     {
  //       title: "App Camera Permission",
  //       message: "App needs access to your camera ",
  //       buttonNeutral: "Ask Me Later",
  //       buttonNegative: "Cancel",
  //       buttonPositive: "OK",
  //     }
  //   );
  //   if (
  //     grantedcamera === PermissionsAndroid.RESULTS.GRANTED &&
  //     grantedstorage === PermissionsAndroid.RESULTS.GRANTED
  //   ) {
  //     console.log("Camera & storage permission given");

  //     var options: any = {
  //       mediaType: "photo", //to allow only photo to select ...no video
  //       saveToPhotos: true, //to store captured photo via camera to photos or else it will be stored in temp folders and will get deleted on temp clear
  //       includeBase64: false,
  //     };

  //     launchCamera(options, (res: any) => {
  //       console.log("Response = ", res);

  //       if (res.didCancel) {
  //         console.log("User cancelled image picker");
  //       } else if (res.error) {
  //         console.log("ImagePicker Error: ", res.error);
  //       } else if (res.customButton) {
  //         console.log("User tapped custom button: ", res.customButton);
  //         alert(res.customButton);
  //       } else {
  //         // let source = res;
  //         // var resourcePath1 = source.assets[0].uri;
  //         const source = { uri: res.uri };
  //         console.log("response", JSON.stringify(res));
  //         console.log("flasdjflasdf;lasdhf", res.assets);
  //         setImgae(res.assets[0].uri);
  //       }
  //     });
  //   } else {
  //     console.log("Camera permission denied");
  //   }
  // };

 
 


