# MPMoviePlayer视频播放控制器
##头文件
需要导入:
```ojbc
#import <MediaPlayer/MediaPlayer.h>
```

## 基本使用
```objc
- (void) tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath{

    // 1.拿到对应的 模型对象
    Video *video = self.videos[indexPath.row];

    // 2.拼接视频的 URL
    NSURL *url = [NSURL URLWithString:[BaseURL stringByAppendingPathComponent:video.url]];

    // 3.创建 视频播放 控制器
    MPMoviePlayerViewController *vc = [[MPMoviePlayerViewController alloc]initWithContentURL:url];
    // 4.modal 弹出控制器
    [self presentViewController:vc animated:YES completion:nil];
}
```
