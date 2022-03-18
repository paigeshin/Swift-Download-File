# Swift-Download-File

```swift

//
//  ViewController.swift
//  Download File
//
//  Created by paige on 2022/03/18.
//

import UIKit

class ViewController: UIViewController {

    lazy var button: UIButton = {
        var btn: UIButton = UIButton()
        btn.translatesAutoresizingMaskIntoConstraints = false
        btn.backgroundColor = .black
        btn.setTitle("Download File", for: .normal)
        btn.addTarget(self, action: #selector(download), for: .touchUpInside)
        return btn
    }()

    lazy var imageView: UIImageView = {
        var iv: UIImageView = UIImageView()
        iv.translatesAutoresizingMaskIntoConstraints = false
        iv.contentMode = .scaleAspectFit
        return iv
    }()

    override func loadView() {
        super.loadView()
        view.addSubview(button)
        view.addSubview(imageView)
        NSLayoutConstraint.activate([
            button.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 30),
            button.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -30),
            button.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -20),
            imageView.topAnchor.constraint(equalTo: view.topAnchor),
            imageView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            imageView.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            imageView.bottomAnchor.constraint(equalTo: button.topAnchor, constant: 20)
        ])
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
    }

    @objc private func download(_ sender: UIButton) {
        guard let url: URL = URL(string: "https://www.ui4u.go.kr/depart/img/content/sub03/img_con03030100_01.jpg") else { return }
        let urlSession: URLSession = URLSession(configuration: .default, delegate: self, delegateQueue: OperationQueue())
        let downloadTask: URLSessionDownloadTask = urlSession.downloadTask(with: url)
        downloadTask.resume()
    }

}

extension ViewController: URLSessionDownloadDelegate {

    func urlSession(_ session: URLSession, downloadTask: URLSessionDownloadTask, didFinishDownloadingTo location: URL) {
        print("File Downloaded - ", location)

        guard let url = downloadTask.originalRequest?.url else {
            return
        }
        let docPath: URL = FileManager.default.urls(for: .cachesDirectory, in: .userDomainMask)[0]
        let destinationFilePath: URL = docPath.appendingPathComponent(url.lastPathComponent)

        // blabla.jpg blabla.mp3...
        // let fileName: String = destinationFilePath.lastPathComponent

        if FileManager.default.fileExists(atPath: destinationFilePath.path) {
            print("File already exists!")
            loadImage(url: destinationFilePath)
            return
        }


        try? FileManager.default.removeItem(at: destinationFilePath)
        try? FileManager.default.copyItem(at: location, to: destinationFilePath)

        loadImage(url: destinationFilePath)
    }

    private func loadImage(url: URL) {
        do {
            let data: Data = try Data(contentsOf: url)
            DispatchQueue.main.async { [weak self] in
                self?.imageView.image = UIImage(data: data)
            }
        } catch {
            print("Error loading image - ", error.localizedDescription)
        }
    }

}


```
