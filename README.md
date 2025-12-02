# Expo Build Documentation

when you face npm expo prebuild error then run this command

create a file in the android folder

- local.properties

write this line in the local.properties file

sdk.dir=/Users/jibonhossen/Library/Android/sdk



when want to create an apk file localy then run this command

expo --profile production --platform android --local


or if you face any error then run this command

ANDROID_HOME=/Users/jibonhossen/Library/Android/sdk eas build --profile production --platform android --local