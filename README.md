# 概要
RoboCup@Home OPL Japan Open 2018 専用タスク、「Go get in unknown environment」のソースコードです。以下に、GoGetItで問われる物体の学習、知識の汎化及び物体の認識について示す。  

# 物体の学習および知識の汎化  
 家庭に新たなロボットが導入される際に、ロボットは人間とのインタラクションを通じて効率的に様々な知識を獲得しなければならない。生活支援ロボットが活躍する未来の家庭では、クラウド環境によるほぼ無限のメモリスペース、GPUやFPGAなどの強力な計算資源があると考えられる。そのため、これらの問題に対して新たに獲得した知識をほぼそのまま簡単なデータ構造に変換して再利用するようなブルートフォース的なアルゴリズムで対応することが可能であると考える。そのため、本タスクでは汎化については考慮せず、簡単な事例ベースで解くアプローチを採用する。  
　本タスクは物体を位置、名称、特徴などで特定できる制約が与えられているため、位置に名称と特徴を紐づけるシンプルなリスト構造を知識表現とし、指示文とデータとのワードマッチングにより位置、名称、特徴を特定する。  
　 
# 物体の認識および把持  
 上記の方法で、名称を特定した後それに対応するオブジェクトの認識を行う。物体の検出はYOLOを用いており、Cup、Bottle、Cylinderといった似た形状の物体ならどんなものでも検出するように事前に学習をさせている。すなわち、形状に関しては高い汎化能力を持つものと考えられる。なお、学習する際はデータ拡張なしで1形状平均1700枚程度の画像で学習を行っている。  
　検出したオブジェクトを切り出した後、Geoffrey Hintonらが提案しているCapsule Network (2017) を用いて物体認識を行う。この手法は比較的少ない学習データ数でロバストな物体認識が可能である。1オブジェクトにつき500枚程度の画像で学習させており、チップスターやビーズ（洗剤）などの具体的な物体名を推論する。  
　次に、物体の3次元把持位置の推定を行う。物体検出をYOLOで行うため、バウンディングボックスの中にはオブジェクト以外に、その背景が映りこむ。そのため、深度情報を用い、当該物体が置かれている机などの平面の除去を行うことにより把持位置の推定精度を高めている。
　最後に、それらの3次元把持位置情報からマニピュレータを制御して物体の把持を行う。  

# 実行方法  
    $(PKG_ROOT)/scripts $ sh ggi_start.sh
    $ roslaunch realsense_camera r200_nodelet_rgbd.launch
    $ roslaunch darknet_ros darknet_ros_gdb.launch

自作パッケージ  
1.物体検出用ノード:[e_object_recognizer](https://github.com/HappyKoyo/e_object_recognizer.git)  
    
2.把持位置検出用ノード:[e_grasping_point_detector](https://github.com/HappyKoyo/e_grasping_position_detector.git)  
  
3.マニピュレーション用ノード:[e_manipulation](https://github.com/HappyKoyo/e_manipulation.git)  
  
